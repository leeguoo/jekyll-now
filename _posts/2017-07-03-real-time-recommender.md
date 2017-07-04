---
layout: post
title: Graph-Based Real-Time Recommender
---

> People don’t know what they want until you show it to them.  
>
>            -Steve Jobs

It is very true for online shopping. When people shop online, many of them have no idea what they are exactly looking for at the beginning. That is why many people, for example my wife, usually spend hours even days in browsing products before they know what they really want to buy. To solve this problem, I built a real-time recommender system to predict target products based on the products they are browsing. 

> If I had asked people what they wanted, they would have said faster horses.  
>
>            -Henry Ford

The basic assumption is that although customers might not know what their target products are, they do know the features of them. For example, people knew they wanted something fast without being aware of the existence of cars. In online shopping, we can assume the features shared by the browsed products are the features of the target products, and the weights are proportional to the number of browsed products (especially newly browsed products) related to it.  

Following these assumptions, I constructed a graph database of products using Neo4j. The nodes are the baby products obtained from Amazon Review Data. Each of them is connected with the products which customers also viewed if they viewed this product. Then, the recommendations are made using the following procedure.
- When a customer clicks the first product, we set the weights of the clicked product and the products connected to it as one, and recommend the connected products.
- When the customer clicks another product, we decrease the old weights by a half, and add one to the weights of the newly clicked product and the products connected to it. This time, we recommend all the products with non-zero weights, and rank them by the weights. 
- We repeat the second step until the customer find the target products.

The video below shows how it works.


<iframe width="560" height="315" src="https://www.youtube.com/embed/NSiL2jWYr54" frameborder="0" allowfullscreen></iframe>
