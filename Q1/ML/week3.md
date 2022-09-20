# Week 3
Last week we did parametric density estimatino. Used Gaussian distribution for each of the classes to estimate the the global parameters on training set. FOr classificaiton use Bayes rule of posterior probability

## Non-parametric density estimation
In real life, it is not easy to define distribution types on data sets. Since we don't no distribution, can't esimate the global parameters.

One way is to use a histogram:
1. split feature in subregions, or bins, of width h
2. COunt number of objects in each bin
3. Estimate the probabily of feature using the formula

Other methods than histogram are:
- Parzen density esimate
- K-Nearest-neighbor density estimate

### Parzen density esimate (Kernel density estimation)
For each data point, we place a *cell* (a.k.a kernel or window function) and add them all up. 

...

We eventually get a distribution but most likely not a known one. 

Several cell shapes to choose from:

- Gaussian
- Box
- Tri
- Triweight

KDE with a box kernel is not the same a histogram: with a KDE with box kernel, you can count all the boxes and determine how many data points, while histogram doesn't represent the # of data points just by looking at the bin.