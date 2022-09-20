# Week 3
Last week we did parametric density estimatino. Used Gaussian distribution for each of the classes to estimate the global parameters on training set. FOr classificaiton use Bayes rule of posterior probability

## Non-parametric density estimation
In real life, it is not easy to define distribution types on data sets. Since we don't no distribution, can't esimate the global parameters.

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