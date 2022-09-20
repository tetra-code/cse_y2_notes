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
