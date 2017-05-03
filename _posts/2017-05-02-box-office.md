Predicting Box Office of Future Movies

What is the risk of investing in a movie? The investors would lose money, if the movie is bad box office. So before making an investment, investors would spend some time to look at the information about the directors, actors, composers… and get a feeling about the box office. 

Here, I make the feeling quantitative. I built a machine learning model which is able to predict box office of future movies based on historical information.

Data Collection and Feature Engineering

By saying historical information I mean the information of past movies. To get this information, I scraped the data of movies (1996-2016) from http://www.boxofficemojo.com.

For each movie, besides numerical features like budget and runtime, there are categorical features like director and actors. To use these categorical feature, The numerical features can be directly used in models. To use the categorical features,  while the categorical features need For each people involved in a movie, I found all the movies he/she was involved 


Model

After getting all the features, I split the data into three parts: training, testing, and hold out. The training data contain the information of movies from 2002 to 2013, they are used to train the model. The testing data contain the information of movies of 2014 and 2015. This part is for engineering features and optimizing hyper parameters. The hold-out data contain the information of movies of 2016. It is the final test of the model. It wasn’t touched until the model is fully established.

The target is log domestic total gross. The algorithm used in this work in Extreme Gradient Boosting Trees (xgboost), and the metric is R squared score.

Feature Selection

There were more than fifty features in the data. Quite some of them are not necessary, and might even hurt the accuracy of the model. To remove those unnecessary features, I used the recursive feature elimination method. 

In this method, I began with all the features, performed the model, obtained the feature importance, and eliminated the least important feature. This process was iterated until there were only five features.

The scores versus the number of features are shown below.

I chose the features which gave the highest score.

Hyper parameter Optimization

I used grid search method to find the optimal hyper parameters. In this model I focused on four hyper parameters: learning rate, max_depth (the maximum depth of each tree), subsample (the percentage of samples for each tree), and colsample_bytree (the percentage of features used in each tree).

Results

The testing score is 0.561, and the hold-out score is 0.586.
The predictions versus the true observations are shown below.



Feature Importance



Ways to Improve



