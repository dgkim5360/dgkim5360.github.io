---
layout: post
title: Why is Logistic Regression called Linear Method?
date: 2018-02-24 23:47:00 +0900
categories:
  - Machine Learning
tags:
  - Classification
  - Logistic Regression
references:
  - The Element of Statistical Learning, 2E, Hastie et al.
  - https://github.com/dgkim5360/the-elements-of-statistical-learning-notebooks#(shameless plug)
---

## tl;dr
Logistic regression is classified as a linear method because its decision boundary is linear, not because we can get a solution of the problem by solving a linear equation (Since it brings MLE problem to solve the logistic regression problem, it generally does not even provide analytic solution).

Logistic regression makes the decision to make sense much more than the plain linear regression when it comes to the classification problem. The reason is that logistic regression tries for the estimtes to mimick the probabilistic behaviors, while linear regression doesn't care that aspects.

## Notebook for more
Below is my jupyter notebook (hosted on [kyso.io](//kyso.io)) containing (a little bit) details of the story. Its original version is on [github repo](//github.com/dgkim5360/the-elements-of-statistical-learning-notebooks/blob/master/articles/why-is-logistic-regression-called-linear-method.ipynb).

<iframe src="https://kyso.io/Don/why-is-logistic-regression-called-linear-method/embed?code=shown" width="100%" height="1000px" frameBorder="0"></iframe>
