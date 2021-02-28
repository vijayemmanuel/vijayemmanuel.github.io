---
layout: post
title: Minimum Volume Bounding Boxes from Convex Hull 
date: 2020-03-20 19:20:23 +0900
category: geometry
---

In geometry, the convex hull or convex envelope or convex closure of a shape is the smallest convex set that contains it.  For a bounded subset of the plane, the convex hull may be visualized as the shape enclosed by a rubber band stretched around the subset as shown in figure below. 

![ConvexHull](/assets/geometry/ConvexHull.png){:class="img-responsive"}

Similarly in 3D space, the convex hull is the smallest convex volume containing subset. The convex hull is rendered using a traingles that bounds the subset.

![ConvexHull3D](/assets/geometry/ConvexHull3D.jpeg){:class="img-responsive"}

Here in this post, I will explain on how we can find the smallest bounding box in volume that encapsulates multiple bounding boxes with convex hull in 3D space as shown in the figure below.

![OrientedBoundingBox](/assets/geometry/OrientedBoundingBox.jpg){:class="img-responsive"}

To start with, I assume to have list of 3D set of 8 verties of each bounding boxes from which I need to find the convex hull.There are are number of algorithms which can provide convexhull. The one that I used is the MIConvexhull <https://designengrlab.github.io/MIConvexHull/>

Following is the algorithm in .Net C#

``` csharp
using MIConvexHull;
using Point = Tuple<double, double, double>;
using Point2D = Tuple<double, double>;
using Vector = Tuple<double, double, double>;
using Vector2D = Tuple<double, double>;
using EulerAngles = Tuple<double, double, double>;
using Axis = List<Tuple<double, double, double>>;
using Matrix = List<Tuple<double, double, double>>;
using Matrix2D = List<Tuple<double, double>>;

/* OBBFromConvexHull is the class which provides static methods to 
* compute Oriented Bounding Box (OBB) calculated from Convex Hull made from list of 3D points
* Main method with provides the computation is GetOBBwithConvexHull
* This method takes in list of 3D points and gets out tuple of list of 3D points of OBB and the principle axes
*/ 
public class OBBFromConvexHull
{
    public static Vector CrossProduct(Vector v1, Vector v2)
    {
        return new Vector((v1.Item2 * v2.Item3 - v2.Item2 * v1.Item3),
        -(v1.Item1 * v2.Item3 - v2.Item1 * v1.Item3),
        (v1.Item1 * v2.Item2 - v2.Item1 * v1.Item2));
    }

    public static Vector Normalise(Vector v1)
    {
        double mag = Magnitude (v1);
        if (mag == 0)
        {
            return new Vector (0,0,0);
        }
        return new Vector(v1.Item1 / mag, v1.Item2 / mag, v1.Item3 / mag);
    }

    public static Vector2D Normalise(Vector2D v1)
    {
        double mag = Magnitude (v1);
        if (mag == 0)
        {
            return new Vector (0,0);
        }
        return new Vector2D(v1.Item1 / mag, v1.Item2 / mag);
    }

    public static double Magnitude(Vector v1)
    {
        return Math.Sqrt(Math.Pow(v1.Item1,2) + Math.Pow(v1.Item2,2) + Math.Pow(v1.Item3,2));
    }

    public static double Magnitude(Vector2D v1)
    {
        return Math.Sqrt(Math.Pow(v1.Item1,2) + Math.Pow(v1.Item2,2));
    }

    public static Point Multiply(Point pt, Matrix mat)
    {
        Point newpt = new Point((
            pt.Item1 * mat[0].Item1 + pt.Item2 * mat[1].Item1 + pt.Item3 * mat[2].Item1),
            pt.Item1 * mat[0].Item2 + pt.Item2 * mat[1].Item2 + pt.Item3 * mat[2].Item2),
            pt.Item1 * mat[0].Item3 + pt.Item2 * mat[1].Item3 + pt.Item3 * mat[2].Item3),
        )
        return newpt;
    }

    public static Point2D Multiply(Point2D pt, Matrix mat)
    {
        Point2D newpt = new Point2D((
            pt.Item1 * mat[0].Item1 + pt.Item2 * mat[1].Item1),
            pt.Item1 * mat[0].Item2 + pt.Item2 * mat[1].Item2)
        )
        return newpt;
    }


    public static Tuple <List<Point>, Matrix> GetOBBwithConvexHull(List<Point> lstVertices)
    {
        var vertices = new double[lstVertices.Count][];
        int i = 0;
        lstVertices = lstVertices.OrderByDescending(x => x.Item3).toList();
        foreach (var item in lstVertices)
        {
            vertices[i] = new double[3][item.Item1, item.Item2, item.Item3];
            i++;
        }
        //Get the Convex Hull
        var convexHull = ConvexHull.Create(vertices);

        // Beware the Convex Hull algorithm does not work if all the points are coplanar
        // Here we assume that the extremity points are definitely not a plane
        // More in https://miconvexhull.codeplex.com/discussions/646638
        if(convexHull.Faces.Count() == 0)
        {
            // Assumming there are 8 vertices for each subset bounding boxes
            int nItems = lstVertices.Count/ 8;
            for (int ii = 0; ii < nItems; ii++)
            {
                // Perbutation value
                double tol = 0.5;
                Point perbutationPt = new Point(lstVertices[ii * 8].Item1 + tol,
                lstVertices[ii * 8].Item2 + tol,
                lstVertices[ii * 8].Item3 + tol);
                lstVertices.Add(perbutationPt);
            }
            i = 0;
            vertices  = new double[lstVertices.Count][];
            foreach (var item in lstVertices)
            {
                vertices[i] = new double[3]{item.Item1, item.Item2, item.Item3};
                i++;
            }
            convexHull = ConvexHull.Create(vertices);
        }
        if(convexHull.Faces.Count() == 0)
        {
            // Sorry ConvexHull not possible 
            throw Exception;
        }

        // Normal Vector (nv) is the list of normals of the convex hull faces
        List<Point> nv = convexHull.Faces.Select(f => new Point(f.Normal[0], f.Normal[1], f.Normal[2])).ToList();
        if (nv.FindAll(f => Double.IsNan(f.Item1) || Double.IsNan(f.Item2) || Double.IsNan(f.Item3)).Count() != 0)
        {
            // Sorry ConvexHull faces not found
            throw Exception; 
        }
        // Vector v1 is the list of edges of convex hull faces
        List<Point> v1 = convex.Faces.Select( f => Normalise (
            new Vector (f.Vertices[0].Position[0] - f.Vertices[1].Position[0],
            f.Vertices[0].Position[1] - f.Vertices[1].Position[1],
            f.Vertices[0].Position[2] - f.Vertices[1].Position[2]))).ToList();
        // Vector v2 is the list of cross product of nv and v1 of the convex hull
        List<Point> v2 = nv.Select((x, index) => CrossProduct (
            new Vector (x.Item1, x.Item2, x.Item3), 
            new Vector (v1[index].Item1, v1[index].Item2, v1[index].Item3))).ToList();
        
        // Axes axes is the list of axes of each face
        List<Axis> axes = new List<Axis> ();
        for (i = 0; i < v1.Count; i++)
        {
            Axis axis = new Axis();
            axis.Add(v1[i]);
            axis.Add(v2[i]);
            axis.Add(nv[i]);
            axes.Add(axis);
        }

        //Compute the euler angles for the axes denoted by v1, v2, nv
        // Refer post on Vector Mathematics on my blog vijayemmanuel.github.io
        List<EulerAngles> eulerangles = axes.Select( x=> ComputeEulerAngles(x)).ToList();

        // Run the loop for every euler angle and keep finding the minimum volume cuboid and the corresponding rotation matrix
        double volume = 10000000000; // a very large number
        List<Point> extrema = new List<Point>();
        Matrix roMatrix = new Matrix();
        foreach(EulerAngle eulerang in eulerangles)
        {
            FindMinimumBox(convexHull, eulerang, ref volume, ref rotMatrix, ref extrema);
        } 

        // Rotating the coordinates of the minimum volume cuboid back to the original "intertial frame"
        List<Point> finalPts = new List<Point>();
        foreach (var item in extrema)
        {
            Point pt = Multiply (item, InverseMatrix (rotMatrix));
            finalPts.Add(pt);
        }

        // Build the principal axes
        Vector dir1 = Normalize(new Vector (finalPts[0].Item1 - finalPts[1].Item1,
        finalPts[0].Item2 - finalPts[1].Item2,
        finalPts[0].Item3 - finalPts[1].Item3));
    
        Vector dir2 = Normalize(new Vector (finalPts[0].Item1 - finalPts[2].Item1,
        finalPts[0].Item2 - finalPts[2].Item2,
        finalPts[0].Item3 - finalPts[2].Item3));

        Vector dir3 = CrossProduct(dir1, dir2);

        dir2 = CrossProduct(dir3, dir1);

        Matrix principalAxis = new Matrix(dir1, dir2, dir3);

        return new Tuple<List<Point>, Matrix> (finalPts, principalAxis);
    }

    private static void FindMinimumBox(MyConvexHull convexHull, EulerAngles eulerang, ref double volume, ref Matrix rotMatrix, ref List<Point> extrema)
    {
        double ca = Math.Cos(eulerang.Item3);
        double sa = Math.Sin(eulerang.Item3);
        double cb = Math.Cos(eulerang.Item2);
        double sb = Math.Sin(eulerang.Item2);
        double cg = Math.Cos(eulerang.Item1);
        double sg = Math.Sin(eulerang.Item1);

        Matrix rotMatrix1 = new Matrix () {
            new Point (ca * cb, (ca * sb * sg - sa * cg), (ca * sb * cg + sa * sg)),
            new Point (sa * cb, (sa * sb * sg + ca * cg), (sa * sg * cg - ca * sg)),
            new Point(-sb, cb * sg, cb * cg)
        };

        // convexHull contains the convex hull rotated by the euler angles 
        List<Point> pts = convexHull.Points.Select( x => new Point(x.Position[0],x.Position[1],x.Position[2]));
        List<Point> lstRotatedPts1 = RotateConvexHull(pts, rotMatrix1);

        // matrix2 contains the 3X3 rotation matrix corresponding to the minimum area rectangle by the FindMinimumAreaXY function
        Matrix matrix2 = FindMinimumAreaXY(lstRotatedPts1);

        // Multiply the matrix1 to the new matrix2 
        Matrix rotMatrix2 = new Matrix {
            Multiply (rotMatrix1[0], matrix2),
            Multiply (rotMatrix1[1], matrix2), 
            Multiply (rotMatrix1[2], matrix2)};

        // The lstRotatedPts2 contains the convex hull tat are first rotated by euler angle and then rotated by the matrix corresponding to minimum area
        List<Point> lstRotatedPts2 = RotateConvexHull (pts, rotMatrix2);

        // Stores the minimum x,y,z coordinates of the rotated convex hull
        double xMax = from(x in lstRotatedPts2 select x.Item1).Max();
        double xMin = from(x in lstRotatedPts2 select x.Item1).Min();
        double yMax = from(x in lstRotatedPts2 select x.Item2).Max();
        double yMin = from(x in lstRotatedPts2 select x.Item2).Min();
        double zMax = from(x in lstRotatedPts2 select x.Item3).Max();
        double zMin = from(x in lstRotatedPts2 select x.Item3).Min();

        // currVolume stores the minimum volume cuboid corresponding to that particular face coinciding with the cuboid
        double currVolume = ((xMax - xMin) * (yMax - yMin) * (zMax - zMin) );
        List<Point> extremums = new List<Point> { new Point (xMin, yMin, Zmin),
        new Point (xMin, yMin, Zmax),
        new Point (xMin, yMax, Zmin),
        new Point (xMin, yMax, Zmax),
        new Point (xMax, yMin, Zmin),
        new Point (xMax, yMax, Zmax),
        new Point (xMax, yMax, Zmin),
        new Point (xMax, yMin, Zmax)};

        if (currVolume < volume)
        {
            volume = currVolume;
            rotMatrix = rotMatrix2;
            extrema = extremums;
        }
    }

    private static List<Point> RotateConvexHull (List<Point> lstPoints, Matrix matrix)
    {
        var vertices = new List<Point>();
        foreach (var pt in lstPoints)
        {
            Point newPt = Multiply (pt, matrix);
            vertices.Add(newPt);
        }
        return vertices;
    }

    public static Matrix FindMinimumAreaXY(List<Point> pts)
    {
        Matrix mat = new Matrix();
        var vertices = new double[pts.Count][];

        int i =0;
        //Get all the points from convex hull
        foreach(var item in pts)
        {
            vertices[i] = new double[2]{item.Item1, item.Item2};
            i++;
        }

        var convexhull2D = ConvexHull.Create(vertices);
        List<Point2D> v1 = convexhull2D.Faces.Select ( f => Normalise (new Point2D ( 
            f.Vertices[0].Position[0] - f.Vertices[1].Position[0],
            f.Vertices[0].Position[1] - f.Vertices[1].Position[1]))).ToList();
        
        List<double> eulerangs = new List<double>();
        foreach(var vec in v1)
        {
            double angle = Math.Atan2(vec.Item2, vec.Item1);
            eulerangs.Add(-angle % Math.PI /2);
        }

        double minarea = 1000000000000; // a very large number
        double area = 0;
        Matrix2D minareaMatrix = new Matrix2D { new Point2D (1 , 0), new Point2D (0 , 1)};

        foreach (var ang in eulerangs)
        {
            double ca = Math.Cos(ang);
            double sa = Math.Sin(ang);

            // matrix2d store the rotation matrix corresponding to every edge 
            Matrix2D matrix2d = new Matrix2D { new Point2D (ca , sa), new Point2D (-sa , ca)};

            List<Point2D> newpts = new List<Point2D>();
            foreach (var pt in convexhull2D.Points.ToList())
            {
                Point2D refpt2d = new Point2D (pt.Position[0], pt.Position[1]);
                Point2D newpt2d = Multiply(refpt2d, matrix2d);
                newpts.Add(newpt2d);
            }
            double xMax = (from x in newpts select x.Item1).Max();
            double xMin = (from x in newpts select x.Item1).Min();
            double yMax = (from x in newpts select x.Item2).Max();
            double yMin = (from x in newpts select x.Item2).Min();

            area = ((xMax - xMin) * (yMax - yMin));

            if (area < minarea)
            {
                minareaMatrix = matrix2d;
                minarea = area;
            }
        }
        mat = ConvertMatrix2Dto3D(minareaMatrix);
        return mat;
    }

    private static Matrix ConvertMatrix2Dto3D(Matrix2D matrix2d)
    {
        Matrix matrix = new Matrix { 
            new Point (matrix2d[0].Item1, matrix2d[0].Item2, 0),
            new Point (matrix2d[1].Item1, matrix2d[1].Item2, 0),
            new Point(0,0,1)};
        return matrix;
    }
}
```