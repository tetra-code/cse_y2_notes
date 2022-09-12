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

