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

## Part 3: Direct Illumination

## Part 4: Global Illumination

## Part 5: Adaptive Sampling
