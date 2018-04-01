---
layout: post
title: A Few Practical Thoughts on Supervised Learning
date: 2018-04-01 13:45:00 +0900
categories:
  - Machine Learning
tags:
  - Classification
  - Logistic Regression
  - Kaggle
  - Regularization
  - Cross-Validation
references:
  - https://www.kaggle.com/dgkim5360/a-few-practical-thoughts-on-supervised-learning
  - https://github.com/dgkim5360/the-elements-of-statistical-learning-notebooks#(shameless plug)
---

## tl;dr
* Dummies surely help to make the model better.
* Logistic regression model might be too simple to check the effect of standardization and regularization.
* Plots with CV results were helpful for assessing the models with regularization.

## Motivation
There are many precious notebooks in Kaggle, (although I have read only a few of most-voted ones), and many of them just used categorical variables, not using dummy variables. And then the comment pointing the use of dummies also got many votes. This is my motivation; how the dummies improve the model?

Since I think that other similar topics could be included here, below are the main questions:

* Nominal variables vs. Dummies
* Does scaling really have effects?
* Then which variable is appropriate to be applied?
* How much does the cross-validation improve the model accuracy?
* Is the regularization really helpful for the generalization?

Here the only logistic regression is used for the training method and there is no comparison among various ones, simply because I do not know much detail of other sophisticated methods. Logistic regression is chosen since it is simple and easy to understand, for me.

## Notebook for more
This Kyso notebook is just a clone of [my Kaggle notebook](https://www.kaggle.com/dgkim5360/a-few-practical-thoughts-on-supervised-learning). If some texts and tables are badly rendered, please check the Kaggle one.

<iframe src="https://kyso.io/Don/kaggle-a-few-practical-thoughts-on-titanic/embed?code=shown" width="100%" height="17182.100000000002px" frameBorder="0"></iframe>
