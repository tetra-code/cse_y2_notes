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
These filters are very simple but powerful. OUr perception is better in dark. IN bright values, hard to detect small changes. In darker values, our eyes are better at detecting. 

Filter basically applies some computation on each pixel and for each color channel on the pixel

The nex filtersize = 2 * Filtersize +1 .  We take a filtersize that corresponds around this region. Reformulating this way allows that that filter region always has an in-pair number and the pixel of interest is always in the center

(2 * filtersize + 1)^2

Try to avoid the pow() function C++ and use the typical math expression.

i and j are pixel positions so we do want them to be integers, but for the sum we want them to be float because?
