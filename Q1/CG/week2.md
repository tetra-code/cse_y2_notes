# Week 2

## Homogenous coordinates
Goal: given 3D point M, we want a function C such that C(M) is the point’s projection in a 2D photo

As mentioned before, homogenous coordinates can represent N-D projective space with N + 1 coordinates in vector forms. Null vectors (0, 0, 0) are not part of the space. 

A group of vectors are considered homogenous coordinates if they satisfy the following condition:

*Two points p,q are equal iff exists a!=0 s.t p * a = q*

Example:
In a 2D projective space of P2:

(2,2,2) = (3,3,3,) = (4,4,4)
(3, 3, 3) != (4, 3, 4)
(0, 1, 0) = (0, 2, 0)

If we have a vector from a standard *vector space* Rn and want to represent this in an N-Dimensional *projective space*, add a 1 to its end. A point (x, y) in R2 embbed in a P2 corresponds to (x, y, 1)

Example:
(1, 4) in R2 => (1, 4, 1) in P2

All points (x, y, 1) form a plane, called as *affine plane*:

![Image](../../images/plane.PNG)

ALl these points are basically lines, simply scaled with a scalar.

(x, y, w) in 2D projection space corresponds to (x/w, y/w) in 2D vector space. Only when the 'w' = 1 is the x, y value same as in both projection and vector space.

Only when w != 0 is there point in the projection space that corresponds to the point in the vector space.

But the points where w = 0 has no correspondence to the vector space. This is because the plane with w = 0 is not reachable. The points with w=0, as you drag w near towards 0, the points go towards infinity:

![Image](../../images/w_0_1.PNG)

![Image](../../images/w_0_2.PNG)

This is a way to describe points at infinity and they converge to that central point:

![Image](../../images/to_infinity.PNG)

## Transformaion with homogenous coordinates
In a vector space, we simply add the other vector for translation:

![Image](../../images/translation_vector.PNG)

In a projection space, we multiply it by a matrix:

![Image](../../images/translation_matrix.PNG)

The above works by adding x with tx, y with ty, and 1 * 1 for the w. Or when w = 2, it is still the same point because remember, we *scale w down to 1 to get the real coordinates*:

![Image](../../images/same_translation.PNG)

Similar but different approach for rotation. This is because with translation, x and y value is only concerned with themselves but for rotation, the value of the other affects the final result:

![Image](../../images/rotation.PNG)

Same if w > 1:

![Image](../../images/w_rotation.PNG)

For points at  inifinity, where (x, y, 0), translation doesn't do anything since infinity will still be in infinity. For points at inifnity when rotation, it actually does rotate

To get a location in infinity space, 
- translate Q to origin
- Rotate around origin
- Translate back to Q

## 3D transformation
In 3D, we have a four coordinates in our vector and all transformations are 4 by 4 matrices. It doesn't change much from the 2D points we used from above, except for rotation.

Translation in 3D:

![Image](../../images/3d_translation.PNG)

Rotation in 3D is a bit more complicated. When we are rotating around the z-axis, the axis z doesn't change; only the y and x. Same concept for y axis and x axis:

![Image](../../images/z_rotation.PNG)
![Image](../../images/x_rotation.PNG)
![Image](../../images/y_rotation.PNG)

The formula to rotate on a point that is not on a axis is very complicated. 
*Don't memorize formula

Since matrix multiplication is not commutative (not all AB != BA), thus order matters. If for some point P we want first scaling as S, and second translation T:

T * S * P = P'         S * T * P != P'

Three main operations to transformation:

- Scale (scale by z axis by 5, multiply the vector (1, 1, 5))
- Translation
- Rotation

To remember:
- Scaling should always come first before any other operation 
- for translation only give value that you want to move. otherwise, 0 for the rest (not 1)
- If an object is on the origin and we rotate by an axis, it will rotate while the object is still in the current location.
- If an object is on the origin and we first translate and then rotate by an axis, the translated coordinates will also be affected by the rotation, thus the translated distance will also be rotated.
- Not possible to achieve same-location rotation while the coordinates is not in the center and rotate by an axis


## Complex objects
Objects are often defined as many components. It is the result of multiple objects at precise coordinaes of each other. We concatenate matrices to place objects. 

Lets say a group of matrices make a complex arm object of multiple components. If we apply a new matrix in the beginning, it will propagate to the rest of the matrices and the whole arm will move based on the new added matrix:

![Image](../../images/propagaion.PNG)

### Order
The Matrix object can store a single transformation or a sequence of transformations. A sequence of transformations is called a composite transformation, which is a result multiplying the matrices of the individual transformations. 

As mentioned before the order matters. Say you want to apply operations to a point P in this order:
1. S
2. R
3. T 

The point p is generally on the far right side of the equation. Thus the order of applying this one by one will be 1, 2, 3. 

For applying the transformation one by one, P is multiplied from right to left:

Left                 Right
<-----------------------
P' = T * R * S * P

For creating a composite transformation and then P, P is multiplied from left to right:

Left                 Right
---------------------->
P' =  (T * R * S) * P

As shown above the direction of the multiplication doesn't affect the result, ONLY THE ORDERING OF THE TRANSFORMATIONS.

When in doubt always assume we are applying one by one thus right to left direction.

### Robot arm
After the cube, when we want to apply an operation to its whole, the operation should be applied at the very last thus after it is finished creating parts for cube. This is to ensure the entire cube is affected by the operation in the end.

When drawing a segment that is influenced by a previous one, operations are involved that take part from the previous segment in order to be influenced by the previous segment's movement as well.

If segment3 exists after segment1 and segment2, the *order of applying the previous operations should be reversed*. For segment3 operation:

1. segment3's scaling
2. segment3's rotation (self position)
3. apply translation from segment2's distance
4. apply rotation from segment2's angle
5. apply segment1's translation
6. apply segment1's rotation

```
    glm::mat4 matrix_segment1 = 
        glm::rotate(id, segment1.rotationX, x_axis) * 
        glm::scale(id, segment1.boxSize) * 
        centering;

    glm::mat4 matrix_segment2 = 
        // Operation to allign with segment1:
        glm::rotate(id, segment1.rotationX, x_axis) * 
        glm::translate(id, { 0, 0, segment1.boxSize.z } ) *

        // Segment2's self operation
        glm::rotate(id, segment2.rotationX, x_axis) * 
        glm::scale(id, segment2.boxSize) * 
        centering;

    glm::mat4 matrix_segment3 =
        // Operation to allign with segment1:
        glm::rotate(id, segment1.rotationX, x_axis) *
        glm::translate(id, glm::vec3 { 0, 0, segment1.boxSize.z }) *

        // Operation to allign with segment2:
        glm::rotate(id, segment2.rotationX, x_axis) *
        glm::translate(id, glm::vec3 { 0, 0, segment2.boxSize.z }) * 

        // Segment3's self operation
        glm::rotate(id, segment3.rotationX, x_axis) * 
        glm::scale(id, segment3.boxSize) * 
        centering;
```

The below image depicts that after finishing applying segment2's rotation, we apply segment1's translation.

![Image](../../images/robot_arm.PNG)

### Solar System
Solar system assignment is similar but has one more condition: orbiting and spinning are two separate operations that should not impact each other in any way.

If the moon has an orbit speed of zero, then it would always stay on the same side of the planet as seen from a static point in space:

![Image](../../images/orbit.PNG)

In order to achieve this, we need to 'offset' the 'static-rotation' that occurs when the moon orbits around the sun. If not, then the moon won't face the same side of the planet when its orbit speed is zero, due to the 'static-rotation' that occurs when rotating the sun:

![Image](../../images/rotation_show.PNG)

Same goes for spinning: if the spin speed were zero then the orientation of the body should not change relative to the orbit. 

In order to achieve the above two, we need to similary 'offset' the 'static-rotation' that occurs when orbiting around the planet. If we didn't do this then the moon's orientation will not be the same, due to the 'static-rotation' that occurs when rotating the planet.

Example of a moon's transformation that orbits around a planet which orbits the static sun:

```
rotationMatrix(computeAngle(planet.orbitPeriod, time), yaxis) *  // orbit(rotate) around sun
translationMatrix(glm::vec3 { planet.orbitAltitude, 0, 0 }) *    // translate to distance from sun
// offset to be on same side of planet when orbit speed is 0
rotationMatrix(-computeAngle(planet.orbitPeriod, time), yaxis) * 

rotationMatrix(computeAngle(body.orbitPeriod, time), yaxis) *   // orbit(rotate) around planet
translationMatrix(glm::vec3 { body.orbitAltitude, 0, 0 }) *     // translate to distance from planet
// offset to achieve static orientation when orbit speed is 0
rotationMatrix(-computeAngle(body.orbitPeriod, time), yaxis) *  /

rotationMatrix(computeAngle(body.spinPeriod, time), yaxis)      // moon static-rotate
```

### Reverse transformation

Remember that (0, 0, 1) vector is the origin in 2D space

All transformation matrixes are guaranteed to have an inverse to go back to. Thus to undo a transformation, multiply the point by the inverse of the transformation. In CS, due to precision issues of floats and doubles, matrix A multiplied by its inverse is not always the identity matrix. Therefore, it is safer to store each transformation matrix result into a stack and to go back certain steps.

![Image](../../images/tree.PNG)

To keep track of the *hierarchical objects* walk over the tree using DFS. Use a stack to keep track of the matrix concatentations along the way:

![Image](../../images/tree_traversal.PNG)

## 1. Transformation, 2. Projection, 3. Viewport

### Projection
After transformation, we have to apply the projection to finally be able to project a 3D point onto our 2D screen. This was straight-forward with our naive approach and using 3-value vector for 3D projection space. 

![Image](../../images/find_m.PNG)

![Image](../../images/calculation.PNG)

But with homogenous coordinates, we require 4-value vectors and not so simple.

![Image](../../images/hard_to_find.PNG)

But there is a solution; Z can easily be removed by scaling by Z. Remember that with homogenous coordinates (x/z, y/z, 1) = (x, y, z). Therefore:

![Image](../../images/solution.PNG)

### Viewport
Not that our point is on our *image plane*, we now have to calculate how large the image is. The image plane has a width and height range of (-1, 1):

![Image](../../images/image_plane.PNG)

The above f stands for *focal length* and it is the distance from the camera to the projection plane. The projection plane is almost always placed at a distance from the camera such that the distance from the *projection plane's center to the projection plane's top and bottom is 1*. This image from this website shows it well: 

![Image](../../images/focal_length.PNG)

*Viewport* is simply mapping our projection onto pixels for dispasly. We take the image plane and transform it into a [0 * width - 1] * [0 * height - 1] matrix called a *viewport*:

![Image](../../images/viewport.PNG)

After finding the matrix *viewport* our camera model is finally complete:

![Image](../../images/camera_model.PNG)