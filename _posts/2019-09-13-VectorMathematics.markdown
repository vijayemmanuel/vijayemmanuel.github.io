---
layout: post
title: Vector Mathematics
date: 2019-09-13 19:20:23 +0900
category: geometry
---

Here I bring you some basic vector which we can use for any 3D geometry data manipulation

## Vector Operations
Let us consider two vectors

$$\vec{v_{1}} = a_{1}x + b_{1}y + c_{1}z$$

$$\vec{v_{2}} = a_{2}x + b_{2}y + c_{2}z$$

Dot Product (scalar value):

$$\vec{v_{1}}.\vec{v_{2}} = a_{1}.a_{2} + b_{1}.b_{2} + c_{1}.c_{2}$$

Additionally we could also express it as where θ  is the angle between vectors

$$\vec{v_{1}}.\vec{v_{2}} = |\vec{v_{1}}||\vec{v_{2}}| cos(\theta)$$

Cross Product (vector value):

$$\vec{v_{1}}\times\vec{v_{2}} = (b_{1}.c_{2} - b_{2}.c_{1})x - (a_{1}.c_{2} - a_{2}.c_{1})y + (a_{1}.b_{2} - a_{2}.b_{1} )z$$


## Understanding Euler Angles

Euler angles provide a way to represent the 3D orientation of an object using a combination of three rotations about different axes.  
Let us take an firghter aircraft. For convenience, we use multiple coordinate frames to describe the orientation of the sensor, including the "inertial frame," the "vehicle-1 frame," the "vehicle-2 frame," and the "body frame."  The inertial frame axes are Earth-fixed, and the body frame axes are aligned with the sensor.  The vehicle-1 and vehicle-2 are intermediary frames used for convenience when illustrating the sequence of operations that take us from the inertial frame to the body frame of the sensor.
It may seem unnecessarily complicated to use four different coordinate frames to describe the orientation of the sensor, but the motivation for doing so will become clear as we proceed.
For clarity, this application note assumes that the sensor is mounted to an aircraft.  All examples and figures are given showing the changing orientation of the aircraft.

### Intertial frame:
The "inertial frame" is an Earth-fixed set of axes that is used as an unmoving reference. 
The sequence of rotations used to represent a given orientation is first yaw, then pitch, and finally roll.

![Intertial Frame](/assets/geometry/Inertial-Frame-1024x655.png){:class="img-responsive"}

### The Vehicle-1 Frame (Yaw Rotation)
As shown in figure below, yaw represents rotation about the inertial-frame z-axis by an angle ψ.  The yaw rotation produces a new coordinate frame where the z-axis is aligned with the inertial frame and the x and y axes are rotated by the yaw angle ψ.  We call this new coordinate frame the vehicle-1 frame.  The orientation of the vehicle-1 frame after yaw rotation is show in Figure 2.  The vehicle-1 frame axes are colored red, while the inertial frame axes are gray.

![Vehicle-1 Frame](/assets/geometry/Vehicle1Frame-1024x654.png){:class="img-responsive"}

Rotation of a vector from the Inertial Frame to the Vehicle-1 Frame can be performed by multiplying the vector by the rotation matrix

$$R^{v1}_{I}(\psi)=\begin{pmatrix}cos(\psi) & sin(\psi) & 0 \\sin(\psi) & cos(\psi) &  0 \\0 &  0 & 1 \end{pmatrix}$$

### The Vehicle-2 Frame (Yaw and Pitch Rotation)
Pitch represents rotation about the vehicle-1 Y-axis by an angle θ as shown in figure below.  For clarity, the inertial-frame axes are not shown.  The vehicle-1 frame axes are shown in gray, and the vehicle-2 axes are shown in red.  It is important to note that pitch is NOT rotation about the inertial-frame Y-axis.

![Vehicle-2 Frame](/assets/geometry/Vehicle2Frame.png){:class="img-responsive"}

The rotation matrix for moving from the vehicle-1 frame to the vehicle-2 frame is given by

$$R^{v2}_{v1}(\theta)=\begin{pmatrix}cos(\theta) &  0 & -sin(\theta)\\0 & 1 &  0 \\sin(\theta) &  0 & cos(\theta) \end{pmatrix}$$

The rotation matrix for moving from the inertial frame to the vehicle-2 frame consists simply of the yaw matrix multiplied by the pitch matrix:

$$R^{v2}_{I}(\theta, \psi)= R^{v2}_{v1}(\theta)R^{v1}_{I}(\psi)$$

### The Body Frame (Yaw, Pitch, and Roll Rotation)
The body frame is the coordinate system that is aligned with the body of the sensor.  On an aircraft, the body frame x-axis typically points out the nose, the y-axis points out the right side of the fuselage, and the z-axis points out the bottom of the fuselage.

The body frame is obtained by performing a rotation by the angle ϕ around the vehicle-2 frame x-axis as shown in figure below.  For clarity, the inertial frame and vehicle-1 frame axes are not shown.  The vehicle-2 frame axes are shown in gray, while the body-frame axes are shown in red.

![Body Frame](/assets/geometry/BodyFrame.png){:class="img-responsive"}

The rotation matrix for moving from the vehicle-2 frame to the body frame is given by

$$R^{B}_{v2}(\phi)=\begin{pmatrix}1 &  0 & 0\\0 &  cos(\phi) & sin(\phi) \\0 &-sin(\phi) & cos(\phi)\end{pmatrix}$$

The complete rotation matrix for moving from the inertial frame to the body frame is given by


$$R^{B}_{I}(\phi, \theta, \psi)= R^{B}_{v2}(\phi)R^{v2}_{v1}(\theta)R^{v1}_{I}(\psi)$$

Performing the multiplication,the complete rotation from the inertial frame to the body frame is given by

$$R^{B}_{I}(\phi, \theta, \psi)=\begin{pmatrix}
cos(\psi)cos(\theta) & cos(\theta)sin(\psi) & -sin(\theta)
\\cos(\psi)sin(\phi)sin(\theta)-cos(\phi)sin(\psi) &  cos(\phi)cos(\psi)+sin(\phi)sin(\psi)sin(\theta) & cos(\theta)sin(\phi) 
\\sin(\phi)sin(\psi)+cos(\phi)cos(\psi)sin(\theta) & cos(\phi)sin(\psi)sin(\theta) - cos(\psi)sin(\phi) & cos(\phi)cos(\theta)\end{pmatrix}$$

The rotation matrix for moving the opposite direction - from the body frame to the inertial frame - is given by

$$R^{I}_{B}(\phi, \theta, \psi)= R^{B}_{v2}(-\phi)R^{v2}_{v1}(-\theta)R^{v1}_{I}(-\psi)$$

Performing the multiplication, the complete rotation from the body frame to the inertial frame is given by

$$R^{I}_{B}(\phi, \theta, \psi)=
\begin{pmatrix}
cos(\psi)cos(\theta) & cos(\psi)sin(\phi)sin(\theta)-cos(\phi)sin(\psi)  & sin(\phi)sin(\psi)+cos(\phi)cos(\psi)sin(\theta)
\\ cos(\theta)sin(\psi) &  cos(\phi)cos(\psi)+sin(\phi)sin(\psi)sin(\theta) & cos(\phi)sin(\psi)sin(\theta) - cos(\psi)sin(\phi) 
\\ -sin(\theta) &  cos(\theta)sin(\phi) & cos(\phi)cos(\theta)
\end{pmatrix}$$

## Principal axes to Euler Angles

The rotational motion of a rigid body is completely defined by tracking the set of principal axes (XB,YB,ZB) with origin at the center of mass, as they turn relative to a set of fixed axes  (XI,YI,ZI)
We can get the Euler angles  if we know the principal axes, P

$$P=
\begin{pmatrix}
A1x & A1y & A1z
\\ A2x & A2y & A2z
\\ A3x & A3y & A3z
\end{pmatrix}$$

A short calculation to fetch the Euler Angles (Rx,Ry,Rz) from P above

``` csharp
Rx,Ry,Rz = 0
if (Abs(A1z + 1.0) <= 0.00001)  // A1z = -1.0
{
    Ry = PI/2.0
    Rx = Atan2(A2x, A3x)
}
else if (Abs(A1z - 1.0) <= 0.00001)   // A1z = +1.0
{
    Ry = -PI/2.0
    Rx = Atan2(-A2x, -A3x)
}
else
{
    if (Abs(A1z) <= 0.00001)       //A1z = 0.0
    {
        //Ry is zero
        Rx = Atan2(A2z, A3z)
        Rz = Atan2(A2y, A1x)
    }
    else
    {
        Ry = -1.0 * Asin(A1z)
        cosY = cos(Ry)

        Rx = Atan2(A2z/cosY, A3z/cosY)
        Rz = Atan2(A1y/cosY, A1x/cosY)
    }
}
return Rx,Ry,Rz
```

## Euclidean transformation of a vector along translation and rotation matrix

The euclidean transforation of a vector can be applied by multiplying it and the transformation matrix.

$$(x',y',z',1) = (x,y,z,1)\begin{pmatrix}
cos(\psi)cos(\theta) & cos(\theta)sin(\psi) & -sin(\theta)  & 0
\\cos(\psi)sin(\phi)sin(\theta)-cos(\phi)sin(\psi) &  cos(\phi)cos(\psi)+sin(\phi)sin(\psi)sin(\theta) & cos(\theta)sin(\phi)  & 0
\\sin(\phi)sin(\psi)+cos(\phi)cos(\psi)sin(\theta) & cos(\phi)sin(\psi)sin(\theta) - cos(\psi)sin(\phi) & cos(\phi)cos(\theta) & 0
\\Tx & Ty & Tz & 1\end{pmatrix}$$

or 

$$x'=x.(cos(\psi)cos(\theta))+ y.(cos(\psi)sin(\phi)sin(\theta)-cos(\phi)sin(\psi))+ z.(sin(\phi)sin(\psi)+cos(\phi)cos(\psi)sin(\theta)) + Tx $$

$$y'=x.(cos(\theta)sin(\psi))+ y.(cos(\phi)cos(\psi)+sin(\phi)sin(\psi)sin(\theta))+ z.(os(\phi)sin(\psi)sin(\theta)) + Ty $$

$$z'=x.(-sin(\theta))+ y.(cos(\theta)sin(\phi))+ z.(cos(\phi)cos(\theta)) + Tz $$


---------------------


*All the mathematics formulae is created with MathJax with this very useful editor <http://mathurl.com/>*


