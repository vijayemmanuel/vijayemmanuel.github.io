---
layout: post
title: Voxel - The 3D Pixel
date: 2020-07-30 19:20:23 +0900
category: geometry
---

A voxel represents a single unit pont on a regularly spaced 3D dimensional grid. You can consider voxel as the equivalet of pixel when it comes to 3D space. Unsually voxels are repesented as cuboids of unit size stacked together. 
Voxels are used in 3D object analysis, clash computation algorithms, 3D redering, 3D printing. 

Here I provide some basic understanding of how we can convert an STL file (another format to represent 3D objects) to voxels. Special credit to this github repository <https://github.com/cpederkoff/stl-to-voxel> from which my understanding is derived 


![EiffelTowerSTL](/assets/geometry/Eiffel_stl.jpg){:class="img-responsive"}

### Step 1: 

STL file represents a 3D file which are bounded by many many bounded triangles. These triangles are represented by three vertices. Read this file and create a collection of 3D vertices of triangles

### Step 2:

We translate  all the 3D vertices of triangles to minima of its vertices and scale them so that it can be fit inside a bounding box specified by the dimensions *resolution* X *resolution* X *true height*  * *scale*

### Step 3:

Now start slicing the traingles along xy plane with a pitch provided by the voxel size

### Step 4:

The traingles are sliced in such a way that we eliminate all the triangle that are planar to the slicing xy plane. We only consider triangles that have vertices above and below the slicing plane or two vertices lying on the plane.

### Step 5:

Find the lines that cut through the triangles. The line endpoints are obtained by interpolating along the triangle vertices.

### Step 6:

For each slicing plane find the pixels that need to be marked "white" if the lines cut the coordinates marked by the grid of voxel size. No "white" means there is no voxel. 


Finally here is the C# implmentation of the converter <https://github.com/vijayemmanuel/Voxelizer>
I also provide a simulation of the voxels generated from a STL file

![EiffelTowerVoxel](/assets/geometry/Eiffel_voxel.jpeg){:class="img-responsive"}







