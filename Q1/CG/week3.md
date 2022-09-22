## Camera Model

Think of it as nto moving the camera but the camera staying still and everything else moving

To go from a 2D space to a 3D space:
The *camera model* matrix multiplicatoin:

pixel mapping (image) *  "standard camera" projection (projection) *  deforms scene (orientation/location)

The flattening takes away the depth 

*frustrum* is frustum is the portion of a solid that lies between one or two parallel planes cutting it.

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


## OpenGL tutorial
With OpenGL the camera is always fixed; we don't move the camera but move the objects. In order to render camear like we see in the slides, you have to set the coordinates so that the triangle doesn't lay on top of the camera: move the triangle away from the origin.

n-2 triangles in a n # of vectors of triangle strip

When transforming an object made up of thousands of triangles, we don't change each individual vectors cause that would be costly. Instead we use the first step from *camera model pipeline*, which is the *ModelView* matrix. Similary don't forget translate so that the object doesn't stay on top of the camera

In OpenGL, it reads in columns (not rows) thus the matrix has to be transposed (just like vector)


WHen you rotate first around an axis, it will move around
when you translate first and then rotate, it will simply rotate in current position

*Normalized device coorindates* is simply making the w of the coordinates 1. 

If triangle entirely inside the cube, we render the entire triangle.
If triangle paritally inside the cube, OpenGL cuts out the outside part and partially render
If triangle outisde the cube, nothing renders
*case where all three points outside cube but the part of the triangle is still inside the cube

OpenGL performs clipping in Clip Space, not in DNC. THis is to avoid doing any for triangle parts that have to be clipped

Because near and far are along the negative z axis in openGL, get the abs values for distance

focal length (f) has inverse relation with field of view (fov). As focal length becomes larger, the field of view becomes smaller. As focal length becomes smaller, te fiedl of view becomes smaller.

As the field of view or angle (Fovy) becomes smaller, our focus becomes narrow thus the object seems bigger. As the angle become bigger, our focus becomes wider thus object seems smaller

THe wider the camera, the shorter the *focal lens* (field of view or FOV)
