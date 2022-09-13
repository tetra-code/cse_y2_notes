# Week2

## Note on notation
In the bayes theorem formula. The class w is discrete (label), the feature x is continous. So
p(x|w), p(x) are probability density function and p(w), p(w|x) are probability mass function. Thus posterior probability will have a probability mass function. 

*for this course we are interested in the probabiliy mass function. IN this couse only work with discrete variables

Back on Bayes theorem, to compute p(x):
p(x) = p(x|y1)p(y1) + p(x|y2)p(y2)


If we are looking for the largest posterior probability, p(x) not explicitely necesssary as both are sides of the comparision are divided by p(x)

## Models and distributions
We don't have a true distriution, only a sampled data set. Thus we have to approximate the class conditional probability density. We can use

- Discriminative models
- Generative models: can create new data from the models
- Parametric models :
- Non-parametric models: don't assume a specific shape of the distrubtion

## Parametric Modeling & Estimation
<!-- Density estimation - uses non-parametic appraoch. -->
*Histogram-based density estimation* approximate desnity by the histogram. Not very accurate

[image](../../histogram_desnity_estimate.PNG)

If for 1-dimensional data, we need about 1000 objects. Lets say this result in 50 parameters (histogram bar)
For M-dimensional (M features) data, we need 1000^M objects. This results in 50^M parameters. Thus this density is not suitable for M>2 features.

Curse of dimensionality: 
1. using more features should give us more information about the outcome. 
2. Densities of these features unknown thus requires estimation. 
3. Number of parameters (histogram bin) increase. 
4. To increase the # of parametesr, need more objects

This results in an optimal number of features to use:

[image](../../curse_dimensionality.PNG)


There are several desity estimation appraoches

- parametric: assume simple global model and estimate the parameters
- non-parametric: assume simple local model, e.g.

*Gaussian Distribution* is equivalent to normal distribution




*Multivariate Gaussians* accounts for p - dimensional density.




The formula above is also denoted as:

N(x|μ, Σ)

The guassian distribution for a 2-dimensional (2 feature) objet data. As shown below, the mean is denoted as vector and the variance is replaced with *covariance matrix*:

[image](../../guassian_2d.PNG)


If the mean vector changes, the shape is shifted. If the covariance matrix changes, the shape is widener, narrower or rotated.

[image](../../rotated_gaussian_2d.PNG)

Below, the arrows are the eigen vectors of the covariance matrix:

[image](../../more_gaussian_2d.PNG)

On convariance matrix, 

[image](../../covariance_matrix_structure.PNG)
## Maximum likelihood

check the formula

## Quiz
Mean of class +1: if you look at the pic, the values that have class +1 are
2  0
3  0

2+3 = 5/2 =2.5. THus mean for feature 1 is 2.5.
0+0 = 0/2 = 0.  Thus mean for feature 2 is 0.

THe covariance of class +1 is a 2 by 2 matrix. The spread of feature 2 is 0. And the spread of feature 2 is 



....
...

##
From above, if you can't get the inverse of the covariance matrix, it might mean you need for objects. Or some feature is redundant and can be removed.

if you have n-dimesnional data set in a gaussian distribution, you need at least n elements for the mean vector and 0.5 * n * (n+1)



 ## Parametric estimation

## MIxture models
You can take combination of separate gaussian distributions. This erquires giving each separate gaussian a weight. 

FOr now ignore mixture models