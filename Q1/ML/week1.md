# Week1

## Introduction to Machine Learning
"No Free Lunch" theorem states that there is no best learning algorithm for all problems. Different problems have different approaches/algorithms that work best

Identify regularies in the world by learning from data (examples). The model should work beyond the specific examples it has been given. Machine learning exists because many tasks are too complicated to explicitely code. It is easier to map from input to output using examples. Because it learns about the world using data, strong connection to statistics, signal processing, and optimisation.

The goal of machine learning is:
- create a model, a mapping from inputs to outputs
- calculate the exepcted loss, how bad the model is on average

![image](../../images/ml_process.PNG)

To represent the object to use in a mathematical function, we need measurements. These are called features (variables, covariates) and depicted as vectors;. This vector space is called *feature space*.

![image](../../images/feature_space.PNG)

![image](../../classifier_evaluation.PNG)

Choosing informative features for the task makes it easy to discriminate between different classes. IF we don't choose right things, can't have a good model. It also depends on what type of classifier we use: linear or non-linear?

To train and test, divide data into *training set* and *test set*
1. Use traning set to fit the model
2. Evaluate performance on the test set

Since test set is an i.i.d (independent and identically distributed) sample from a data set, it generally gives a good estimate of the performance.

Types of machine learning:
- *supervised learning*: classification, regression - label exists

![image](../../supervised_learning.PNG)

- *unsupervised learning*: dimensionality reduction, clustering - no output label is given

![image](../../unsupervised_learning.PNG)

- *reinforcement learning*: selecting optimal actions

*for this course the focus is supervised and unsupervised learning

## Learning from examples
Machine learning is basically feeding data (examples) to a model and tweeking its parameters so that it become more accurate with its predictions on non-example test data.