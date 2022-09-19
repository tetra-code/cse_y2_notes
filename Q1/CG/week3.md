# Last time 

## Camera Model

Think of it as nto moving the camera but the camera staying still and everything else moving

To go from a 2D space to a 3D space:
The *camera model* matrix multiplicatoin:

pixel mapping (image) *  "standard camera" projection (projection) *  deforms scene (orientation/location)

The flattening takes away the depth value

## Depth value
In a 3D infinite scene, how do we represent Z? -> add a *near plane* and *far plane*

Sometimes in games or simulatinos, things pop up out of nowhere. This is because these things were in the far plane (which we don't care) and then . Nowadays doesn't happen often and instead it is replaced with a fog or something to hide it

# Week 3
NOrmally we project based on a square (not the true size of our screen) When a screen is wider, squeeze the entire scene by a certain factor along the x axis (width). This scaling is done at the END, not beginning

The z axis we see inside the (box) are negative axis.

(this purple dot may seem like its half between green and blue thus 0 but acutally not. we don't know. it is anywhere from -1 < z < 1 ) it is not the actual distance of camera observer

it is -near and -far as it i son the negative z axis

IN order:
- The MOdelview Matrix takes the local origin into the 3D space. 
- PRojection
- Viewport (take values -1 1 and maps )

The above multiplied together gets us a single 4 x 4 camera matrix

Sometimes you can get near an object and can at some point see inside of it. THat's because you cross the 'near plane'



The above works well for but fails for triangles. For points (vertices ) that are outside of our *cross frustum*, we need to test triangles and clip it . THis only necessary for outside (Crosses) our frustum. Everything inside is taken care of

After projection a triangle is a cube and after if they are not inside -1 and 1, then we know they are outside. 

Pixels are filled if the center is covered. If it is a partial, 

## Making things beautiful
Triangles can have different colors on vertices. THe colors are interpolated over the triangle (graident). This is also called (Rasterization). *aliasing* is when you hide the original represetation with a lower representation (too many to be captured on the pixels). SOMetimes flickering happens when the resolution of the screen/camera can't keep up with resolution of the fine object.

If pixels are partially covered, cliasitng can occur. *super sampling* allows averaging the  . Lose resolution but looks smoother (also known as box filter)


## lighting
when cos goes close to 0, does at perfect light reflecting positoi, you cos bceomes 1 thus get the full effect of the lightning