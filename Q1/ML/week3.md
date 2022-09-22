# Week 3
Last week we did parametric density estimatino. Used Gaussian distribution for each of the classes to estimate the global parameters on training set. FOr classificaiton use Bayes rule of posterior probability

## Non-parametric density estimation
In real life, it is not easy to define distribution types on data sets. Since we don't no distribution, can't esimate the global parameters. And the decision boundaries no longer simple shapes

One way is to use a histogram:
1. split feature in subregions, or bins, of width h
2. COunt number of objects in each bin
3. Estimate the probabily of feature using the formula

Other methods than histogram are:
- Parzen density esimate
- K-Nearest-neighbor density estimate

*non-parametric might be more accurate when optimized since it gets the average of every data set and don't assume a pre distribution type

### Parzen density esimate (Kernel density estimation)
For each data point, we place a *cell* (a.k.a kernel function or window function) and add them all up. 

...

We eventually get a distribution but most likely not a known one. 

Several cell shapes to choose from:

- Gaussian
- Box
- Tri
- Triweight

KDE with a box kernel is not the same a histogram: with a KDE with box kernel, you can count all the boxes and determine how many data points, while histogram doesn't represent the # of data points just by looking at the bin.

Example
Given a set of four data points, lets say we want to find the probability of feature x=3 happening. Use the kernel function with width h=1 to find hte Parzen PFD.

WE are not estimating the global parameters for our data. The parameter h is not estimated from our data, thus non-parametric approach. To get the most optimal h, you have some candidates of h and test how well which h is good at classifying.

Whether which kernel function works best, that is up to you to experiment

The width parameter or h matters. This requires optimization for your specific problem. 
For the part that overlaps, our threshold determines the decision boundary.


### K-nearest-distribution
Basic idea is to use 'closeness' to classify the data point. Here 'k' is the number of data points to consider.

Locate the cell on the new point x but the cell shape is fixed. do not change the cell shape. Grow the cell untill it covers k objects.

Give training examples {xi, yi}:
- xi attribute - value representation of examples
- yi class label - ex apple, 0, 1, etc
- testing point x that we want to classify
- The algorithm:
1. compute distance D(x, xi) to EVERY training example xi
2. Select k closest instances xi - xk and their labels y1 - yk
3. output class y which is most frequent within the y1 - yk (majority vote)

If k = all the x values (whole data set), we are basically looking at class priors (everything classified as the most probablye class)
If k = 1, highly variable and result in unstable decision boundaries (any noise will have consequences)

To determine the right value of k is similar to choosing the right value of h from *kernel density estimation*:
1. set aside a portion of training data for validation (test data set)
2. vary k
3. pick k that gives best performance

If we have equal number of positive/negative neighbours:
- choose an odd k (doesn't solve multi class)
- randomly decide positive or negative
- pick class with greater prior
- use 1-nn classifier to decide

To measure the distance:
- Euclidean (numeric features)
- manhattan distance
- hamming distance (categorical features)

Feature scaling is scaling all your features into a certain range. This is necessary as it might make your boudnary decision more easier to look.

pros and cons:

## Naive Bayes Classifiers

(recap)

*curse of dimensionality*
When estimating a class conditional probability density, each feature vector x has many features. To estimate this joint pdf, we need lots of data

In *Naive Bayes* we avoid the curse of dimensionality by strongly assuming that ALL features are INDEPENDENT. This allows us to multiply the probabilities of each feature and this will give us the class conditional probability density:

𝑝 𝑥 𝑦 = 𝑝 𝑥1, 𝑥2, 𝑥3, 𝑥4, ... , 𝑥𝑑|𝑦 = ෑ𝑖=1
𝑑
𝑝 𝑥𝑖 𝑦
= 𝑝 𝑥1 𝑦 𝑝 𝑥2 𝑦 ... 𝑝(𝑥𝑑|𝑦

For example:
We assume conditional independence of two variables (both features) given a third variable (the class)

Example: probablity of going to the beach and having a heartstroke may be
independent if we know the wheather is hot
𝑝 𝐵, 𝑆|𝐻 = 𝑝 𝐵|𝐻 𝑝 𝑆 𝐻
• Hot weather “explains” all the dependence between beach and heartstroke
• In classification: class value explains all the dependence between features

Navie bayes clasifier can be used in both: use parametric density function (bayes theorem to calculate the parameters) or use non-parametric density function. 

So with the same data set, we can get two different diviosn class

(read up on continuous data example)

Problem with naive bayes:
If the features covary, they aren't indenpendent and naive bayes strong assumes indenpendence.

## Discriminiative classifiers
There are 2 types of classifiers
- gnerative classifiers (parametic densitives, non-parametric densitites)
- discriminiative classifiers (support vector, decision tree, neural network)

Discriminative models draw boundaries in the data space, while generative models try to model how data is placed throughout the space.

![Image](../../images/discrminiatve_generative.png)

Small datasets require models that have low complexity (or high bias) to avoid overfitting the model to the data. the simpler the machine learning algorithm, the better it will learn from small data sets. Naive Bayes algorithm is among the simplest classifiers and as a result learns remarkably well from relatively small data sets. 

For very small datasets, Bayesian methods are generally the best in class, although the results can be sensitive to your choice of prior. I think that the naive Bayes classifier and ridge regression are the best predictive model

Small datasets, you need models that have few parameters (low complexity) and/or a strong prior. You can also interpret the “prior” as an assumption you can make on how the data behaves.

Misclassification cost, 


NEed independent data (data not used in training).Assume random subset of the data in the graph and another random subset are independent. But doesn't sometimes hold, when time is a factor(frame based detection in video, and all these deep detection algorithms fail). SO make sure you use different types of videos for testing, not just a few ones



When selecting your object features, it is very important which feature you choose so that 
it represents a true distribution type in real life. If you had a model that can detect well certain object A but then started introducing only 'outliers' in the training data set, it is very likely that your model will become worse at predicting, because only ourlier filled training data set is not a true representation of the distribution of real life occurences.

## ML learning curve
As size of the training set increases, the classification error also increases. But this isn't linear. At some point, the more data you have the more likely it will truly represent the distriution type of real life occurence for that object 

*Bayes error* is smaller than the asympototic error - bayes eror assuems ideal distribution

## Bias variance tradeoff
When given some data, we could get unlucky and get a-typical data. TO say something gneral, we need to average over different training sets. 
