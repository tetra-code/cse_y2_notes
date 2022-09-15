## Homogenous coordinates


## Transformation and PRojection
In a projective space, we would liek to have matrix M s.

In R2, a vector (x, y)
In P2, a vector (x, y, 1)

M(x, y, 1) = (x+t, y +m, 1)\

To tranlate in R2 simply add vecotr x,y and t, m

TO translate in p2, multiple a matrix
[1, 0 , 0]
[0, 1, 0]
[t, m, 1]

by vector [x, y, 1]. This will equal to (x+t, y +m, 1)\

Similar for rotation as well

(look at slides for equation)

For points at  inifinity, where (x, y, 0), translation doesn't do anything since infinity will still be in infinity

For points at inifnity when rotation, it actually does rotate

To get a location in infinity space, 
- translate Q to origin
- Rotate around origin
- Translate back to Q

## 3D
In 3 dimension, we have a four coordinate in our vector and all transformations are 4 by 4 matrices. It doesn't change much from the 2D points we used from above, except for rotation. 

When we are rotationg around the z-axis, the axis z doesn't change; only the y and x. Same concept for y axis and x axis

The formula to rotating around on a point that is not on a axis is very complicated.s
Don't try to memorize the formula but make a drawing and try to make sense from it

Since matrix multiplication is not commutative (not all AB != BA), thus order matters. If for some point P we want
1. scaling as S
2. translation T

T*S*P = P'    NOT S*T*P

## complex objects
Objects are often defined as many components. It is the result of multiple objects at prcise coordinaes each other. We concatenate matrices to place objects

We want a matri that hwne applied to objects, it shifts them to their right coordinates to build a comlete object

Lets say a group of matrices make a complex arm object of multiple components. If we apply a new matrix in the beginning, it will propagate the entire matrices and the whole arm will move bawsed on matrix

Remember that
(0, 0, 1) vector is the origin in 2D space

How to remember order

TRTTR (0, 1, 1) will end up in that place since it is 1 y value above *from where it is facing*, not our point of view
If you come from the right the origin is always the reference



In this order(T R T R T R), apply to P. The order may seem the opposite and they are. but when we concate the multiple matrices to creat ea composite matrix transformation, it reads from left to right:

1. RP , rotate around the origin
2. TRP, move the vertex not the origin
3. RTRP, rotate around the origin
4. TRTRP
5. RTRTRP
6. TRTRTRP 

Basically P' = (T*R*T*R*T*R)*P 
not ()


All transformation matrixes are guaranteed to have an inverse to go back to. THus to undo a transformation, by theory multiply by inverse of the transformations. IN CS, due to precision issues of floats and doubles, matrix A multiply by its iverse ins not always the identity matrix. Therefore, it is safer to store each transformation matrix result into a stack and to go back certain steps, pop the stack

