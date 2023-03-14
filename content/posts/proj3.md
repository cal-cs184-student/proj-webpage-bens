---
title: "Project 3 Part 1: PathTracer"
date: 2023-03-08
# weight: 1
# aliases: ["/first"]
# tags: ["first"]
author: ["Ben Cuan", "Ben Lin"]
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: true
description: https://github.com/cal-cs184-student/proj-webpage-bens || https://github.com/cal-cs184-student/p2-pathtracer-sp23-bens3
# canonicalURL: "https://canonical.url/to/page"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
cover:
    image: "/proj2/p2-t3-02.jpg" # image path/url
    alt: "proj2 cover" # alt text
    # caption: "asdfasdasfd" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
---


## Overview
TODO

## Part 1: Ray Generation and Scene Intersection

In this part, we implemented ray generation from the camera perspective, as well as ray-triangle intersections and ray-sphere intersections.

The ray generation pipeline is as follows:
1. We determine the $(x, y)$ coordinates in screen space of a particular pixel we would like to cast a ray into.
2. Using Monte Carlo sampling, we randomly generate a ray at uniform random through the region bounded by $(x, y)$ and $(x+1), (y+1)$.
3. We pass the normalized (between 0 and 1) screen coordinates into the `Camera::generate_ray()` function.
4. In `generate_ray()`, we transform the provided screen space coordinates into world space coordinates by first transforming into camera space using the FOV values, then into world space by rotating by the `c2w` transform.
5. If applicable, we check for intersections with objects. If an intersection is detected, we terminate the ray at the intersection (set `max_t` to the intersection time).

Our triangle intersection algorithm worked in three parts, based on the [Möller-Trumbore algorithm](https://cs184.eecs.berkeley.edu/sp23/lecture/9-22/intro-to-ray-tracing-and-acceler):
1. Determine when the ray will intersect with the plane the triangle is on, or if it will not intersect at all, by calculating the plane normal via a cross product of two edges. Terminate immediately if the ray is parallel to the plane.
2. Check that point where the ray intersects the plane is within the triangle and the `t` value is between `min_t` and `max_t` using barycentric coordinates. $\alpha, \beta$ can be calculated using a weighted dot product of orthogonal vectors based on the intersection point and triangle edges.
3. Calculate the normal vector using barycentric coordinates, weighting the vertex normals $n1, n2, n3$ by $\alpha, \beta, \gamma$ respectively.

#### IMAGES

Cow:
![cow](/proj3/cow.png)
Banana:
![banana](/proj3/banana.png)
Sphere:
![sphere](/proj3/CBspheres.png)

## Part 2: Bounding Volume Hierarchy

### Creating the BVH
In this part, we implemented the BVH acceleration structure to dramatically increase the performance of path tracing.

The algorithm for generating the bounding volume hierarchy is as follows:
1. First, check if the current node is a leaf node. If so, simply set its `start` and `end` values, then return it. Otherwise, it's an inner node, so we should continue with the algorithm to split.
2. Find the centroid of the current bounding box.
3. Create six lists corresponding to the objects to the left and right of each axis of the centroid.
4. Iterate over all objects in the current box, and add them to three of the six lists depending on its position. (For example, if the centroid is $(1,1,1)$ and an object is at $(0,0,2)$, it should be added to `x_left`, `y_left`, and `z_right`).
5. Select the axis with the most even split (greatest size of the smallest list out of the left and right).
6. Create a left and right node with the left and right lists of the selected axis, and pass these into two recursive calls.

Here is a screenshot of the teapot mesh demonstrating the appearance of the BVHNodes:
![p3](/proj3/p3-2.png)

### Demos

Demos were rendered on an Ubuntu 22.04 virtual machine with 6 Ryzen 7 3700x cores, 16GB RAM, and a GTX 1080.

**Lucy**
 - 133,796 primitives
 - Rendering Time: 0.0767s
 - Average speed: 5.6005 million rays per second
 - Intersection tests per ray: 1.784651

![lucy](/proj3/CBlucy.png)


**Dragon**
 - 110,012 primitives
 - Rendering Time: 0.0930s
 - Average speed: 4.9570 million rays per second
 - Intersection tests per ray: 3.377663

![dragon](/proj3/CBdragon.png)


## Part 3: Direct Illumination

We wrote two direct lighting functions: one using uniform hemisphere sampling and the other using importance sampling.
For uniform hemisphere sampling, we took `num_sample` sample rays eminating from the hit point and uniformly chosen from the hemisphere around the hit point.
The algorithm works as follows for each sample ray:
1. Determine if the ray will intersect with another object in the scene using `intersect` and save the intersection in `light_isect`. 
2. If there is an intersecion we calculate the contribution to our object's brightness at the hit point using the reflection equation. The values used are in the next 3 steps
3. $f_r$ is determined using `bsdf->f` of the object.
3. $L$ is determined by the by the emission of the intersected light. If the object the sample ray intersects with is not a light, then it contributes no brightness.
4. The angle is determined using `cos_theta` of the sample ray's direction
5. We add the contribution of this sample to `L_out`
Once we calculate the contribution of all of the samples, we normalize by the number of samples and return that result as the brightness of the object at the hit point.

For importance sampling, we iterated through all of the light sources in the scene, and estimated their contribution to the lighting of the object.
The algorithm works as follows for each light source:
1. If it is a point light we take only one sample, otherwise we take `ns_area_light` samples
2. For each sample we determine whether the light is behind the object and if there is anything blocking the light in the following two steps
3. We trace a ray from the light to the hit point, if the distance is positive then we know that the light is behind the object
4. We check if there is an object between the object and the light using `intersect` with the `max_t` set to the distance from the light to the object. If we do not intersect anything, then we can add the light's contribution to the brightness of the hit point, normalized by the pdf for that light and the number of samples.
Once we calculate the contribution from all of the light sources we return that result as the brightness of the object at the hit point.

### Demos
Bunny wih direct illumination and hemispheric sampling
![CBbunnyH](/proj3/CBbunnyH.png)

Bunny with direct illumination and importance sampling
![CBbunnyI](/proj3/CBbunnyI.png)

Hemispheric sampling takes much longer to resolve compared to Importance sampling


## Part 4: Global Illumination

We implemented indirect lighting via a monte carlo estimator with russian roulette for random termination. Our `at_least_one_bouce_radiance` function works recursively where we determine how much light is emitted from an object by calling `at_least_one_bounce_radiance` on it. The algorithm works as follows:
1. Base Case: When there are no further bounces, we return the `one_bounce_radiance` of the object at the hit point according to the algorithms from part 3.
2. Recursive Case: We randomly sample a ray eminating from the hit point and if it hits an object, we use the reflectance equation to determine the contribution of that intersected object to our hit point. The light of the intersected object is calculated recursively using `at_least_one_bounce_radiance`.
We keep count of each recursion and terminate at the base case when there are no further bounces. Additionally, we have a chance to randomly terminate based on our Russian Roulette constant. Finally, if the sampled ray has no intersection, the recursion terminates.
## Part 5: Adaptive Sampling


