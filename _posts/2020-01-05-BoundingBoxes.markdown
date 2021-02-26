---
layout: post
title: Problems with Bounding Boxes
date: 2020-01-05 19:20:23 +0900
category: geometry
---

In this post, I present you some common problems dealing with bounding boxes which needs to manitpulate in 3D space

------

**Problem 1**: I have bounding box coordinates for a rigid body in its "inertial frame". I would like to apply transformation over this box to get its new coordinates. 

- The bounding box size coordinates is given

$$(X'max, X'min, Y'max, Y'min, Z'max, Z'min) $$

- The Euler Angles for the new position is given

$$R^{B}_{I}(\phi, \theta, \psi)$$

- The Translated position for the new position is given

$$(Tx , Ty , Tz)$$

**Solution**
We know the tansformation matrix  is given by the formula (Refer post on [Vector Mathematics]({% post_url 2019-09-13-VectorMathematics %}))

$$(x',y',z',1) = (x,y,z,1)\begin{pmatrix}
cos(\psi)cos(\theta) & cos(\theta)sin(\psi) & -sin(\theta)  & 0
\\cos(\psi)sin(\phi)sin(\theta)-cos(\phi)sin(\psi) &  cos(\phi)cos(\psi)+sin(\phi)sin(\psi)sin(\theta) & cos(\theta)sin(\phi)  & 0
\\sin(\phi)sin(\psi)+cos(\phi)cos(\psi)sin(\theta) & cos(\phi)sin(\psi)sin(\theta) - cos(\psi)sin(\phi) & cos(\phi)cos(\theta) & 0
\\Tx & Ty & Tz & 1\end{pmatrix}$$

Let us get the CoG position of the bounding box so that we can transform with the help of transformation matrix above.

$$X'cl = (X'max + X'min)/2 $$

$$Y'cl = (Y'max + Y'min)/2 $$

$$Z'cl = (Z'max + Z'min)/2 $$

The new position of the CoG is given by 

$$(Xcl,Ycl,Zcl,1) = (X'cl,Y'cl,Z'cl,1)\begin{pmatrix}
cos(\psi)cos(\theta) & cos(\theta)sin(\psi) & -sin(\theta)  & 0
\\cos(\psi)sin(\phi)sin(\theta)-cos(\phi)sin(\psi) &  cos(\phi)cos(\psi)+sin(\phi)sin(\psi)sin(\theta) & cos(\theta)sin(\phi)  & 0
\\sin(\phi)sin(\psi)+cos(\phi)cos(\psi)sin(\theta) & cos(\phi)sin(\psi)sin(\theta) - cos(\psi)sin(\phi) & cos(\phi)cos(\theta) & 0
\\Tx & Ty & Tz & 1\end{pmatrix}$$

Similarly we can derive the transformed bounding box maximum and minimum coordinate value

$$(Xmax,Ymax,Zmax,1) = (X'max,Y'max,Z'max,1)\begin{pmatrix}
cos(\psi)cos(\theta) & cos(\theta)sin(\psi) & -sin(\theta)  & 0
\\cos(\psi)sin(\phi)sin(\theta)-cos(\phi)sin(\psi) &  cos(\phi)cos(\psi)+sin(\phi)sin(\psi)sin(\theta) & cos(\theta)sin(\phi)  & 0
\\sin(\phi)sin(\psi)+cos(\phi)cos(\psi)sin(\theta) & cos(\phi)sin(\psi)sin(\theta) - cos(\psi)sin(\phi) & cos(\phi)cos(\theta) & 0
\\Tx & Ty & Tz & 1\end{pmatrix}$$

$$(Xmin,Ymin,Zmin,1) = (X'min,Y'min,Z'min,1)\begin{pmatrix}
cos(\psi)cos(\theta) & cos(\theta)sin(\psi) & -sin(\theta)  & 0
\\cos(\psi)sin(\phi)sin(\theta)-cos(\phi)sin(\psi) &  cos(\phi)cos(\psi)+sin(\phi)sin(\psi)sin(\theta) & cos(\theta)sin(\phi)  & 0
\\sin(\phi)sin(\psi)+cos(\phi)cos(\psi)sin(\theta) & cos(\phi)sin(\psi)sin(\theta) - cos(\psi)sin(\phi) & cos(\phi)cos(\theta) & 0
\\Tx & Ty & Tz & 1\end{pmatrix}$$

Similary the other coordinates can be calculated for 

$$(X'min,Y'min,Z'max)$$

$$(X'min,Y'max,Z'min)$$

$$(X'max,Y'min,Z'max)$$

$$(X'min,Y'max,Z'max)$$

$$(X'max,Y'max,Z'min)$$

$$(X'max,Y'min,Z'min)$$

-----

**Problem 2**: I have a bounding box oriented in 3D space. I would like to know the dimensions of the bounding box
- The maximum and minimum extremum values is given. 

$$(Xmax,Ymax,Zmax)$$

$$(Xmin,Ymin,Zmin)$$

- The Euler Angles for the this position is given

$$R^{B}_{I}(\phi, \theta, \psi)$$

**Solution**

We can find the major diagonal from the extremum values

$$\vec{v_{1}} = (Xmax,Ymax,Zmax)$$

$$\vec{v_{2}} = (Xmin,Ymin,Zmin)$$

$$\vec{V_{diag}} = \vec{v_{1}} - \vec{v_{2}}$$

$$\vec{V_{diag}} = (Xmax-Xmin,Ymax-Ymin,Zmax-Zmin)$$

Now we need to transform the diagonal vector to "intertial frame" by applying the inverse transformation matrix [Vector Mathematics]({% post_url 2019-09-13-VectorMathematics %}))

$$\vec{V'_{diag}} = \vec{V_{diag}}.
\begin{pmatrix}
cos(\psi)cos(\theta) & cos(\psi)sin(\phi)sin(\theta)-cos(\phi)sin(\psi)  & sin(\phi)sin(\psi)+cos(\phi)cos(\psi)sin(\theta)
\\ cos(\theta)sin(\psi) &  cos(\phi)cos(\psi)+sin(\phi)sin(\psi)sin(\theta) & cos(\phi)sin(\psi)sin(\theta) - cos(\psi)sin(\phi) 
\\ -sin(\theta) &  cos(\theta)sin(\phi) & cos(\phi)cos(\theta)
\end{pmatrix}$$

Now we know that the contributions of each component of the "intertial frame" to a unit vector in that direction is given by the direction cosines. As we know 

$$|\vec{a}|.cos(\theta) = (\vec{a}.\vec{b})/|\vec{b}|$$

Here we can do the same. 

$$ Dimensions  = (\vec{V_{diag}}.\vec{V'_{diag}})/|\vec{V'_{diag}}|$$

-----
