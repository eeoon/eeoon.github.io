---
layout: post
title: "Robot Coordinate Systems and Euler Angles"
date: 2021-03-26 09:00:00 +0900
description: "Cartesian, cylindrical, and spherical coordinate systems and Euler angles, with their differences and characteristics from a robotics perspective"
tags: [coordinates, mathematics]
categories: tech-note
giscus_comments: false
related_posts: false
---

![Coord_planes_color.svg](/assets/img/blog/mathematics/coordinates/image1.svg){:.lead width="800" height="100" loading="lazy"}

- Coordinate systems are one of the important concepts in robotics because they represent positions in the space a robot moves through.
- To perform tasks such as motion control, localization, and mission execution, a robot must know its current position and target position precisely.
- A lack of this information can cause various problems, such as collisions for mobile robots or safety issues for industrial robots.
- Robots use a variety of coordinate systems. This post covers the types of coordinate systems, their differences, and their characteristics.
- Although not a coordinate system itself, Euler angles are also covered.

# Orthogonal coordinates

**Cartesian coordinate system**

![Cartesian-coordinate-system.svg](/assets/img/blog/mathematics/coordinates/image2.svg){:.lead width="800" height="100" loading="lazy"}

The Cartesian coordinate system is the most commonly used way of representing a point in space in geometry, and it is the basic coordinate system we are all familiar with.

- 2D coordinate system
    - Represents a point on a plane. The system consists of a horizontal x-axis and a vertical y-axis.
    - A point's position is written as (x,y).
- 3D coordinate system
    - Represents a point in space. The system consists of a horizontal x-axis, a vertical y-axis, and a z-axis perpendicular to the plane formed by the x and y axes.
    - A point's position is written as (x,y,z).

**Polar coordinate system**

![Polar_graph_paper.svg](/assets/img/blog/mathematics/coordinates/image3.svg){:.lead width="800" height="100" loading="lazy"}

The polar coordinate system represents a point on a plane by a radius and an azimuth angle, and it can be converted to Cartesian coordinates using trigonometric functions.

- Radius (r) : the distance from the origin to the point.
- Azimuth (θ) : the angle measured from the positive x-axis, in radians or degrees. (Radians are generally used.)

A point is therefore written as (r,θ), and converting to Cartesian coordinates gives (r * cos(θ) , r * sin(θ)). Polar coordinates are effective for problems with circular symmetry and are frequently used in engineering fields such as gravitation and electromagnetism.

- Conversion from polar to Cartesian coordinates :

$$
x = rcos(\theta) \\
y = rsin(\theta)
$$

**Cylindrical coordinate system**

![Cylindrical_coordinate.gif](/assets/img/blog/mathematics/coordinates/image4.gif){:.lead width="800" height="100" loading="lazy"}

The cylindrical coordinate system expresses a point's position by its distance from the origin, an azimuth angle, and a height.

- Radius (r) : the horizontal distance from the origin; the straight-line distance between the origin and the point.
- Azimuth (θ) : the angle measured from the positive x-axis, in radians or degrees.
- Height (z) : the vertical distance from the origin.

A point is therefore written as (r,θ,z). It is often used to describe cylinders and cylindrical structures, and in physics, engineering, and computer graphics.

- Conversion from Cartesian to cylindrical coordinates :

$$
r = \sqrt{x^2 + y^2} \\
\theta = tan^{-1}\frac{y}{x} \\
z = z
$$

- Conversion from cylindrical to Cartesian coordinates :

$$
x = rcos(\theta) \\
y = rsin(\theta) \\
z = z
$$

**Spherical coordinate system**

![Spherical_coordinate.gif](/assets/img/blog/mathematics/coordinates/image5.gif){:.lead width="800" height="100" loading="lazy"}

The spherical coordinate system defines a point's position by its distance from the center of a sphere, an azimuth angle, and a polar (elevation) angle. It is defined in 3D space and is used to place points on a sphere.

- Radius (r) : the distance from the center of the sphere to the point.
- Azimuth (θ) : the angle measured from the positive x-axis. Radians are generally used.
- Polar angle (φ) : the angle measured from the x-y plane. Also generally expressed in radians.

A point is therefore written as (r,θ,φ). Since spherical coordinates represent a point on a sphere, they are useful for analyzing celestial bodies such as the Earth or the Sun, and are frequently used in astronomy.

- Conversion from Cartesian to spherical coordinates:

$$
r = \sqrt{x^2 + y^2 + z^2} \\
\theta = arccos\frac{z}{r} \\
\phi = arctan\frac{y}{x} \\
$$

- Conversion from spherical to Cartesian coordinates:

$$
x = rsin(\theta)cos(\phi) \\
y = rsin(\theta)sin(\phi) \\
z = rcos(\theta)
$$

# Euler Angle

<!--
![3_dimesion_coordinates](/assets/img/blog/mathematics/coordinates/image6.png){:.lead width="800" height="100" loading="lazy"}|
-->
![Eulerangles.svg](/assets/img/blog/mathematics/coordinates/image7.svg)


So what are the Euler angles mentioned in robotics? Euler angles describe the orientation of a rigid body as rotations of a 3D coordinate system. They are used together with the Cartesian coordinate system; as in the figure on the right, when the current frame is denoted by uppercase (X,Y,Z), the orientation can be expressed by **ψ** (psi), **θ** (theta), and **φ** (phi).

- **ψ** (psi) : the angle by which the x-y plane is rotated about the z-axis
- **θ** (theta) : the angle by which the z-y plane is rotated about the rotated x-axis (N-axis)
- **φ** (phi) : the angle by which the x-y plane is rotated about the rotated z-axis (Z-axis)

Alternatively, considering only rotation about each individual axis, rotation about the X-axis is called **roll**, rotation about the Y-axis **pitch**, and rotation about the Z-axis **yaw**. 

The difference between Euler angles and quaternions will be covered in a future post.
