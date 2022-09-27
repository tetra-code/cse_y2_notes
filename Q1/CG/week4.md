# Week 4

## Interpolation on a trinagle
To interpolate on a triangle, one should describe point P on the triangle as a combination of the three vertices that make up the triangle. For the three vertices A, B, C and coefficients x, y, z:

P = xA + yB + zC, with x + y + z = 1 & x,y,z>0

The coefficients determine the appropriate mix of corresponding vertex attributes. For each point on the triangle the coefficients are different. 

![Image](../../images/triangle_interpolation.PNG)

If the point P was directly in the center of the triangle the coefficients will be x = y = z = 1/3.

Interpolation is used for rasterization. But if the triangle doesn't cover the center of a pixel, than that pixel won't be colored.

![Image](../../images/great_tv.PNG)

## Textures