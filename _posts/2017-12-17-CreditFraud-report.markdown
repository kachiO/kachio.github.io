---
layout: post
title: "Kaggle Credit Card Fraud Detection Dataset"
comments: true
date: 2017-12-17
excerpt: "Kaggle Credit Card Fraud Detection using Linear SVM, One Class SVM, and Autoencoder"
mathjax: true
---

## [View notebook via nbviewer](https://nbviewer.jupyter.org/github/kachiO/ipython-notebooks/blob/master/CreditFraud-report.ipynb)

# Kaggle Credit Card Fraud Detection Dataset
The datasets contains transactions made by credit cards in September 2013 by european cardholders. This dataset presents transactions that occurred in two days, where we have 492 frauds out of 284,807 transactions.

Features V1, V2, ... V28 are the principal components obtained with PCA, the only features which have not been transformed with PCA are 'Time' and 'Amount'. The feature 'Time' contains the seconds elapsed between each transaction and the first transaction in the dataset. The feature 'Amount' is the transaction Amount, this feature can be used for example-dependant cost-senstive learning. The Feature 'Class' is the class label, where 1 is a fraudlent transaction and 0 is a normal transcation.
[Data Source](https://www.kaggle.com/dalpozz/creditcardfraud)

Here are the methods I will apply to this problem:
- Shallow machine learning classifier: Linear Support Vector Machine (SVM)
- Anomaly detection algorithm: One Class SVM    
- Neural network: autoencoder

**Libraries Used**: pandas, numpy, scikit-learn, keras, matplotlib, plotly, and seaborn


## Precision & Recall Primer

Given the severe class imbalance (0.17% of fraudulent transactions), rather than measuring accuracy I will use precision and recall to evaluate the performance of each method. I will also plot the normalized confusion matrix to help visualize performance of each method.

Precision measures the classifier's ability not to label a negative sample as positive:

$$ Precision =  \frac{TP}{TP+FP} $$

Recall measures the classifier's ability to find all the positive samples of a class, in other words the true positive rate:

$$ Recall = \frac{TP}{TP+FN} $$

where *TP* is true positive, *FN* false negative, and *FP* false positive.

For fraud detection, I want a model with high recall, i.e. a model that captures all fraud transactions. Also, I want a model with minimal false positive rate, which would most likely depend on the goals of the business. High precision is not that important for this particular exercise, so long as the false positive (or false alarm) rate is kept low, for instance less than 5%. Again this would depend on the business goals/application.


## TL;DR Summary

The goal of this exercise was to detect fraud transactions in a public dataset. I compared the performance (recall score) of three machine learning methods: linear SVM, one class SVM, and an autoencoder. The autoencoder model achieved the highest recall score, suggesting that an autoencoder neural network model could be useful and novel approach to fraud (and perhaps anomaly) direction.

## [View notebook via nbviewer](https://nbviewer.jupyter.org/github/kachiO/ipython-notebooks/blob/master/CreditFraud-report.ipynb)
