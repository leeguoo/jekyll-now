---
layout: post
title: Rescale Amazon Ratings Using Reviews
---

## Ratings are not always reliable

In collaborative recommender systems, all the recommendations rely on customer ratings. The question is “are the ratings reliable?” 

Let’s see an example. Here is a review written by an Amazon customer: 

>“this dress is beautiful, my daughter have two of this dresses one in purple and this green one. the only problem is the price, its crazy!!!! Healthtex is a walmart brand so i purchased both dresses at walmart and for this crazy price i can buy 3 of this dresses at walmart and i don't have to pay shipping!!” 

What do you think is the rating associated with this review? I guessed a rating of two or three stars would be suitable, because the customer complained a lot about the price. However, the customer put a five-star rating on it. Apparently this customer and I have different standards of ratings, and he or she is more generous. 

Here is another review from Amazon:

>“This book is so cute and it really made me feel warm inside to turn the pages and discover various animals and their moms playing together. We have more in common with animals than we might at first think. A perfect and adorable animal picture book for Mother's Day!”

This is a very positive review, but the associated rating is only one star. This is no more a disagreement on standards, I consider it as a misrating.

The above findings are not surprising. We’ve seen a lot of these anomalous ratings on many websites. This is not a big deal for human beings. We can just ignore the ratings and rely on the knowledge we learn from the reviews. However, for recommender systems, which purely rely on ratings, the anomaly could be a very severe problem preventing accurate recommendations. 

## Rescale ratings

To solve this problem, a practical strategy is to build a model which can mimic human beings’ ability to read reviews and rescale ratings accordingly. Following this strategy, I experimented with a supervised learning model to learn the “common sense” about the relationship between ratings and review contents, and replaced the raw ratings with predictions of the model. Ideally, these new ratings will follow a universal (or averaged) standard and are not affected by outliers like misratings. 

In the experiment, I selected the Amazon reviews and ratings of 2014 for the products in the category of “kid & baby”, and split them into a training set and a test set. The review texts were transformed into a binary feature matrix, in which the rows and columns are corresponding to reviews and the unique words appeared in all the training reviews, respectively. Each matrix element is either 1 or 0, indicating whether the word exists in the review. Training a Bernoulli Naive model with the training set and evaluating it with the test set results in classification scores as below.

![](https://raw.githubusercontent.com/leeguoo/leeguoo.github.io/43fd72046c44ac61efc69210bb0c23756aab1bc3/images/2017-06-09-rescale-review/scores.png?raw=true)

Given the simplicity of the model, these scores are fairly good. It is interesting that one- and five-star ratings are much easier to distinguish than two-, three- and four-star ratings, consistent with our impression about ratings. 

After sorting the reviews by the difference between raw and rescaled ratings, the two examples mentioned at the beginning of this post are ranked at the top and the bottom of the review list, respectively. (This is actually the reason they were chosen as examples.) The very positive review for the animal picture book is now associated with a five-star rating, showing the ability of the model to correct misratings. The other review for the Healthtex dress is now associated with a one-star rating. In my opinion, this rescaled rating is too low. Although the customer complained a lot about the price, he or she seemed still satisfied with the dress. But I can’t blame my model for this. It takes single words as independent features. Apparently, the positive word “beautiful” is overwhelmed by the strong negative word “crazy”. And it is not able to capture the positivity hidden in the phrases like “the only problem” and “my daughter have two of this dresses”. 

To improve the model, I will need to create more features using techniques like ngram, count vectorizer, TFIDF, LDA, LSI, NMF, and clustering. Beyond that, it makes more sense to treat ratings as numbers rather than independent categories. A regression model might do a better job than the classification model I experimented here.  

## Important Words to Distinguish Ratings

Besides simplicity, the other reason I chose a Naive Bayes model is its interpretability. Since Naive Bayes models can easily take care of huge number of features, I can keep all the unique words and find out which are the most important words to distinguish different ratings. 

The below table shows the ten most frequent words, ranked by their probabilities (P(word | rating)), in each rating.   

![](https://raw.githubusercontent.com/leeguoo/leeguoo.github.io/43fd72046c44ac61efc69210bb0c23756aab1bc3/images/2017-06-09-rescale-review/freq_words.png?raw=true)

Obviously, to distinguish ratings, we can’t rely on these most frequent words. We need to extract the words which are more frequent in one rating than in others. To achieve that, I calculate adjusted probabilities by dividing the probabilities of the words in each rating by the sum of their probabilities in all ratings. The top ten words ranked by the adjusted probabilities in each rating are shown in the below table.

![](https://raw.githubusercontent.com/leeguoo/leeguoo.github.io/43fd72046c44ac61efc69210bb0c23756aab1bc3/images/2017-06-09-rescale-review/impt_words.png?raw=true)

Now the patter is much more clear!
