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