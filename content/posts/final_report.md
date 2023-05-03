---
title: "CS184 Final Project Report"
date: 2023-05-03
# weight: 1
# aliases: ["/first"]
# tags: ["first"]
author: ["Ben Cuan", "Ben Lin", "Joel Rodiel-Lucero", "Ishaan Dham"]
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: true
description: Raymarching
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
    image: "/final/forever.png" # image path/url
    alt: "final project cover" # alt text
    # caption: "asdfasdasfd" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
---

## Abstract

This project is a music visualizer using a sea of morphing shapes to capture the essence of the song. The shapes are rendered to create an infinite sea of shapes using raymarching. The sea of shapes visualize the different aspects of the music in the following ways:
* The size of each shape reflects the volume of the song
* The shapes rotate to the beat of the song
* The spectrum of the song is visualized in the bottom left corner
The project was coded in Unity using shaders. In `Raymarch.shader` each ray is cast out from the camera until the distance between steps, as calculated by `DistanceEstimator`, is sufficiently small. Then the object is rendered with blinn-phong shading. `DistanceEstimator` uses the SDF of the given shape to determine the distance to that shape. The volume and spectrogram were BENCUAN

## Technical Approach

### Techniques and Algorithms

* **Raymarching**: We used the technique of Raymarching in order to solve the rendering problem. Building off the idea of raytracing we learned in class, we implemented raymarching like so:
    1. Just like raytracing, we start from the camera's origin point and a camera's set direction.
    2. We loop through all objects in the world and calculate the signed distance between it and the origin point. By choosing the smallest distance, this gives us an idea of which object we will see first in the rendering process.
    3. We can visualize this distance as the radius of a circle. If the radius is bigger than _MINDIST_, we repeat another step of the raymarching process, but this time the origin point lays *on* the end of the circle. We simply advance the previous origin point by the calculated signed distance by the camera's direction.
    4. We repeat steps 2-3 until the signed distance is smaller than _MINDIST_. We can interpret this as the final collision hit point. With the final accumulated distance, the advanced origin point and the direction, we can find the point.
* **Unity + GLSL Shading**: We chose to use Unity Game Engine in order to visualize our raymarching techniques. It's ability to quickly render and present GLSL shaders in real-time made this our best choice. We used GLSL shaders in order to implement raymarching through the fragment shader.
* **Audio visualization**:
* **Implicit SDF's**: Each type of object was rendered using it's SDF (Signed Distance Function). We found the SDFs for various shapes online (https://iquilezles.org/articles/distfunctions/) and used them to define the shapes we chose. We ended up rendering the following shapes: sphere, cube, octahedron, torus, and solid angle. 
* **SDF Transformations**:

### Problems Encountered

After creating our prototype of an endless field of spheres using raymarching, we were unable to create an endless terrain. After our unsuccessful attempt, we decided to enhance our visualizer using different shapes and other changes to the endless field. In the end, we believe that we created an effective and highly engaging music visualizer, even though it was not what we had originally had in mind.

### Lessons Learned

## Results

### References

Basic Raymarching and diffuse shading:
https://michaelwalczyk.com/blog-ray-marching.html

SDF Formulas:
https://iquilezles.org/articles/distfunctions

Random Noise:
https://forum.unity.com/threads/generate-random-float-between-0-and-1-in-shader.610810/

Transforms:
https://jamie-wong.com/2016/07/15/ray-marching-signed-distance-functions/
https://www.shadertoy.com/view/4tcGDr

Volume:

Spectrum Analyzer:

### Member Contributions

#### Ben Cuan

#### Ben Lin
Implemented the SDFs for octahedron and torus. Also implemented finding the normal for shading, and diffuse shading.

#### Joel Rodiel-Lucero
Worked on infinite terrain raymarching. Implemented Blinn-Phong shading. Implemented radial gradiant background visualization. Implemented the SDF for Solid Angle.

#### Ishaan Dham
Implemented morphing of different shapes. Worked on infinite sphere raymarching shader.