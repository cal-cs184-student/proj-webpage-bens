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

This project is a music visualizer using a sea of morphing shapes to capture the volume and tune of the song. The shapes are rendered to create an infinite sea of shapes using raymarching. The sea of shapes visualize the different aspects of the music in the following ways:
* The size of each shape reflects the volume of the song
* The color of each shape reflects the spectrogram of the song
* The shapes rotate to the beat of the song
The project was coded in Unity using shaders. In `Raymarch.shader` each ray is cast out from the camera until the distance between steps, as calculated by `DistanceEstimator`, is sufficiently small. Then the object is rendered with blinn-phong shading. `DistanceEstimator` uses the SDF of the given shape to determine the distance to that shape. The volume and spectrogram were 

## Technical Approach

### Techniques and Algorithms

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

#### Ishaan Dham