# week 4

## Vectors and Shading
Remember that vector is not the same as position. Position is a point on a space. A vector is a direction from point A to point B. Just because they are both in the form of glm::vec3 in C++ does NOT mean same thing.

To get a vector going in the direction of point A:
```
point.A - point.B
```

If we did point.B - point.A, it is going in the direction of point B

When we calculate the dot product of two products, we are calculating their cosO, with O being the angle. Lets say we have a normal vector from a surface point and a light vector. In order to make sure that the light is only reflected ABOVE the surface and NOT BENEATH the surface:

```
    if (glm::dot(normal, lightVector) < 0.0)
        return { 0.0, 0.0, 0.0 }; 
```

This is because if the angle between normal vector and a vector is between 90 ~ 270 degrees (beneath the surface), cosO is [-1, 0]. Therefore, any dot product that is less than 0.0 can be dark.


For the shading assignment, the light, reflection, half-way, view (camera) vector ALL have to me normalized.
(L, R, H, V)

cross product of two vectors returns a new vector that is perpendicular to both vectors. But keep in mind that this result in a vector, which starts in the origin (0, 0, 0). If you wanted to use this result vector to get to a point, the selected position must be added to the vector (thing of it as 'new' origin). In addition, make sure the vector you use in cross product is in right direction.

The result of every 

## Others

Domain is the texture space where we retrieve the data from. There are different domain dimensions 1D 2D 3D (GPUs support up to 3D)

Store the loop of a texture and save computation by a lookup in a texture (go to texture space see what the approximate value is in the table). Go further and even apply interpolation to increase the accuracy

THe *Lookup Table*. Instead of calculating texture values, implement via a 1D texture lookup. 

## Dynamic textures
Render an image and store it as a textures