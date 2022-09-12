# Image

## Formula

## Channel
An image from a standard digital camera will have a red, green and blue channel, thus 3 channels in total. A grayscale image has just 1 channel. 

## Pixel
To access a specific pixel (i, j), lets say (3, 4), find the channel 

With image information we need some information about the width.

 lets say (3, 4), 

One channel (grayscale) image (resolution 8 x 5) mean it doesn't contain RGB values but only grayscale values. So instead of multiplying by 3 we multiply by 1 (width)

(j * width + i)

3*8 + 3

IF it is RGB, 3 * (4 * 8  + 3)

TO find a pixel(i, j) give n index I for a graysacle image (resolution 3x3), index I=8

solution: 
i = I%width // modulo == remainder of division
j = I/width                     

Always check for out of bounds. Unless the compiler is in debug mode, it will not check/test whether it is in bound and it will fail. When you work with tables, always test yourself whether you are inside the range or not to be safe

# Filter

## Box filter
These filters are very simple but powerful. OUr perception is better in dark. IN bright values, hard to detect small changes. In darker values, our eyes are better at detecting. 

Filter basically applies some computation on each pixel and for each color channel on the pixel

The nex filtersize = 2 * Filtersize +1 .  We take a filtersize that corresponds around this region. Reformulating this way allows that that filter region always has an in-pair number and the pixel of interest is always in the center

(2 * filtersize + 1)^2

Try to avoid the pow() function C++ and use the typical math expression.

i and j are pixel positions so we do want them to be integers, but for the sum we want them to be float because?


We need to first make a copy of the image and then store the pixel result in the copied image, to prevent averaging wheat you already averaged

## Boom filter
1. start original image
2. threshold the image (only keep large color values like > 0.9). Depending on the image, instead of immediately dropping down any below threshold value to 0, have a function that makes this transtion smoother.
3. Apply box filter on thresholded image. THis will result a bit darker image. If too dark, multiply by scalar to scale up the highlighted values.
4. Add the highlighted values to the original

The square box from the boxfilter may be visilbe on the result. You can also have a generla filter. THe sum of the values in the filter image should always be 1, to maintain brightness level.


Macine learning is simply a successive filterin and thresholding process.

## Storing
There are several formats for images. Two major categories:

- Compress data but lose details. Relatively small space - JPEG
- Lossless but require more space - PPM

### PPM
PPM image consists of a header and iamge data. Header specifies all meta data. It contains for example:

- magic number, e.g. P3 for human readable (the big array part) pixel values in RGB format
- IMage width, whtie space, IMage hieght, white space
- Maximum color value between [0, 65535]. MOre pricision than 8 bit

IN the example, you will see that 15 is the hightest number in any pixel. THus max color value is 15.

PPM is simple but inefficient in storage. THe size directly relates to the bytes per color channel (if not human-readable)
and resolution

### JPEG
JPEG is much more complicated than PPM. you won't have to reproduce JPEG images

Basically trying to represent the functions within an image

COmpression by reducing the quality (lossy). THe compression ratio 1:10 still results in high quality images. THe main idea is to use frequencey decomposition and remove high frequency first. THis means that if the. 

FOr each of the functions in the image, the coefficient values of the functions that change very little will be 'larger' and the ones that change frequently will be 'small'

Because we discard very small changes, the quality has limitations. You can see very closely in JPG files that when zoomed in the transition is not smooth

When working with JPEG images, it requires more memory in RAM compared to storage in HARD DISK

## Linking ALgebra with GPU
It takes a triangle and projects it onto a screen. Linear perspective: haveing one point where everything is converging. This was present since middle ages art

On a checker board from a linear perspective, how to find the distances between these horizontal lines. When the checkboard turns, the central point will lie somewhere on a horizontal line. The intersection from the original point lines (check the picture for detailed explanation). YOu can do the same for another point on the opposite side and you can create *two point perspective*.

## Linear perspective
It is relatively easy because with projective geometry everyting is lienar using homongeous equations.

## Virtual Camera model
Given a 3d point, find a function that results in the points projection in the photo. As the camera moves or the object moves, the image will be the same. ON the graphics card, the image is always the same, we just change our perspective. 

To translate an image (vertices), add a vector to our vertices.

To rotate an iamge, use matrix multiplication with the cos, sin matrix

Combine these to calculate the perspective projection. For the computer to calculate where to project it on the screen. In 2D perspective: calculate v by using the ratio of similar triangles. IMage:

Naive operation to project a scene point with the camera:
Apply camera position (add offset)
Aply rotation (matrix mul)
APply projection (non linear scaling)

This can become complicated. A better way is to unify translation, rotation and projection. But because translations and projections are not linear  we can't use matrixes. Instead use homogenous coordinates from projective geometry. 

A camera projection is a matri and combining matrices allows us to define hierarchical-object dependencies.

## HOmogenous coordinates
An N-dimensional projective space Pn is represented by N+1 coordinates. No null vectors

Two points p,q are equal iff exists a != 0 s.t p*a = q 

IN a 2D projective space P2,
(2,2,2) = (3,3,3,) = (4,4,4)
(3, 3, 3) != (4, 3, 4)
(0, 1, 0) = (0, 2, 0)

To embed a standard vectors space Rn into an n-D projective space Pn, we can map
Take Rn add a 1 to its end 

A point (x, y) in R2 embbed in a projective space corresponds to (x, y, 1). All points (x, y, 1) form a plane, called as *affine plane*. ALl these poitns are basically lines, simply scaled with a scalar.

Thus we can go back to R2 by dividing the corodintes by the last entry. (x, y, w) in P2 corresponds to (x/w, y/w) R2. But the poitns where you have zero in the end, has no correspondence to R2. The plane with w=0 is not reachable. THese points with w=0, as you drag w near towards 0, the points go towards infinity. THis is away to decribe poitns at infinit, and they converge to that central point! This isthe key to transform ojcets using projective geometry

