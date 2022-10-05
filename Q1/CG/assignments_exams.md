# Assignment

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

# Exam tips

- must bring object to origin first in order to rotate around another object and then translate again. 
For points q,p and p rotatates around q, we must make p 'in-position' to rotate around the origin. 
Thus translate p by negative q coordinate. This will make the p 'in-position' to rotate around the origin which can be seens as 
rotating around the q, just in different location. If we don't do this it will NOT rotate as a circle
around the origin.

- Order always on the right for first operation. direction of the multiplication doesn't 
affect the result, ONLY THE ORDERING OF THE TRANSFORMATIONS. When in doubt always assume we are applying one by one thus right to left direction.

- question for bike, the absoute value angle part is to consider that bike will move 1 unit forward
when pedaled full 360 degrees. iT IS absolute since we are pedal goes clockwise.

- focal length, is when the projection plane's center to top distance is one and projection 
plane's center to bottom distance is also one. To find focal length, find the projection plane coordinate such 
that the length from its center to top is 1 and center to bottom is also 1

- Remember that black pixel + white pixel does NOT equal gray. as 0.0 + 1.0 = 1.0. Same for gray: black pixel + gray pixel =

black + white = white
black + black = black
black + gray = gray
gray + white = white

- To check if any point is inside triangle, make it into a linear (matrix multiplication) form
and do the gaussian elimination

- To see if a point lies on a line (A, B) of the triangle, create the  'interpolation' form
and the triangle point that does NOT make the line, it's coefficient is 0 (not 1)

- Scaling an object that is centered at the origin produces a different result than scaling an object that has been moved away 
from the origin. 

- For last question in shadows, it is the shadow map and thus you apply the scale and filter and then compute the coordinate values

- Remember that for diffuse and specular, you have to get the sum of each light value present, not just one. For ambient it is all uniform so doesn't matter.

- near plane and image plane are DIFFERENT things. Focal length f applies to image plane. image plane isn't needed in OpenGL.