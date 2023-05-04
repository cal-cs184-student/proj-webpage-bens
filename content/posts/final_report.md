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
description: Endless Raymarching Music Visualizer
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
    image: "/final/finalsplash.png" # image path/url
    alt: "final project cover" # alt text
    # caption: "asdfasdasfd" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
---

## Abstract


![splash](/final/finalsplash.png)

This project is a music visualizer using a sea of morphing shapes to capture the essence of the song. The shapes are rendered to create an infinite sea of shapes using raymarching. The sea of shapes visualize the different aspects of the music in the following ways:
* The size of each shape reflects the volume of the song
* The shapes rotate to the beat of the song
* The spectrum of the song is visualized in the bottom left corner
The project was coded in Unity using shaders. In `Raymarch.shader` each ray is cast out from the camera until the distance between steps, as calculated by `DistanceEstimator`, is sufficiently small. Then the object is rendered with Blinn-Phong shading. `DistanceEstimator` uses the SDF (signed distance function) of the given shape to determine the distance to that shape. The volume and spectrogram are handled through Unity scripts and passed through to the shader as material parameters.

## Technical Approach

### Techniques and Algorithms

#### Unity/GLSL Shading
We chose to use Unity Game Engine in order to visualize our raymarching techniques. It's ability to quickly render and present GLSL shaders in real-time made this our best choice. We used GLSL shaders in order to implement raymarching through the fragment shader and the ShaderLab framework.

#### Raymarching
We used the technique of Raymarching in order to solve the rendering problem. Building off the idea of raytracing we learned in class, we implemented raymarching like so:
    1. Just like raytracing, we start from the camera's origin point and a camera's set direction.
    2. We loop through all objects in the world and calculate the signed distance between it and the origin point. By choosing the smallest distance, this gives us an idea of which object we will see first in the rendering process.
    3. We can visualize this distance as the radius of a circle. If the radius is bigger than _MINDIST_, we repeat another step of the raymarching process, but this time the origin point lays *on* the end of the circle. We simply advance the previous origin point by the calculated signed distance by the camera's direction.
    4. We repeat steps 2-3 until the signed distance is smaller than _MINDIST_. We can interpret this as the final collision hit point. With the final accumulated distance, the advanced origin point and the direction, we can find the point.

![raymarch](/final/raymarching.png)

Here is the code for iterating through the steps of raymarching:
```c
float totalDistance = 0.0;
int steps;
for (steps = 0; steps < MAXSTEPS; steps++) {
    float3 p = from + totalDistance * direction;
    float dist = DistanceEstimator(p);
    totalDistance += dist;
    if (dist < MINDIST) {
        // Color the object
    }
}
// Color the background
```

#### Audio Visualization
We used Unity's built-in `GetSpectrumData()` function to extract the spectrogram data from our audio clips. We then aggregated the data into 8 frequency bins and passed this data into both the canvas to show the visualization, and the shader file.

To get the loudness at the current time, we fetched data from the `audioSource` object in the scene. This data could then be passed into ShaderLab using `material.SetFloat()`.

These parameters were then used in the shader code to perform several transformations.

![spectrogram](/final/spectrogram.gif)



#### Implicit SDF's
Each type of object was rendered using its SDF (Signed Distance Function). We found the SDFs for various shapes online (https://iquilezles.org/articles/distfunctions/) and used them to define the shapes we chose. We ended up rendering the following shapes: sphere, cube, octahedron, torus, and solid angle. 

SDF's take in a position vector and some additional parameters (like size/radius), and returns a float orthogonal distance to the surface of the shape it represents. This makes it very easy to implicitly define objects using functions, rather than having to explicitly create meshes as is required for raytracing. Each step along the ray was defined as the orthogonal distance to the object. Thus, once the step size became small, we knew that the ray had collided with the object's surface.

SDF's are inherently very simple in terms of code. For example, a sphere is defined with the single-line function below:

```c
float sdSphere(float3 p, float r) {
    return length(p) - r;
}
```

#### SDF Transformations

To augment different shapes together we used `opSmoothUnion` as defined below:

```c
float opSmoothUnion(float d1, float d2, float k) {
    float h = clamp(0.5 + 0.5*(d2-d1)/k, 0.0, 1.0 );
    return lerp(d2, d1, h) - k*h*(1.0-h);
}
```

This function essentially performs a linear interpolation on the distances calculated from two different object sdfs, which creates a smooth join as illustrated in this image:

![smooth union](/final/smoothunion.png)


#### Blinn-Phong Shading
For shading we calculated the normal by calculating a gradient around the desired point, and then normalizing this gradient vector to create the unit normal vector. Using the normal vector we implemented Blinn-Phong shading as we learned in class as a sum of the background color, the diffuse shading, and a specular highlight. 
For the background shading we did a linear interpolation between the base color and the hue shifted color. We changed the linear interpolation based on a sine wave derived from the shader's time.


### Problems Encountered

After creating our prototype of an endless field of spheres using raymarching, we were unable to create an endless terrain due to the difficulty of integrating it with our raymarching algorithm. After our unsuccessful attempt, we decided to enhance our visualizer using different shapes and other changes to the endless field instead. In the end, we believe that we created an effective and highly engaging music visualizer, even though it was not what we had originally had in mind.

### Lessons Learned

The biggest take-away from this project was acquiring the ability to build, use, and modify an effective raymarching engine from scratch. Working with raymarching required completely changing our perspective on rendering, and there was a significant learning curve around using implicitly-defined shapes for all calculations rather than explicitly creating meshes, which both rasterization and ray tracing require. 

We also learned a great deal about the strengths and weaknesses of raymarching through our various attempts to add features. We found that once we picked up the concept of an SDF, it was incredibly easy to create geometric shapes and modify them using transforms such as scaling, rotating, or morphing with other shapes. Smooth morphing especially would have been very difficult to do with traditional rendering methods, but could be accomplished in only a few lines using raymarching. However, raymarching does not seem very suitable for complex shapes or meshes, as we were unable to create the scenery we originally intended to (e.g. hills, grass, clouds...).

## Results

* Here is our [code](https://github.com/64bitpandas/eternal-marcher).

* Here is a [video demo](https://drive.google.com/file/d/180I8JnpMHcHjdbu86mt10KKre7bCHNuD/view) of the music visualizer.

* Here is a [video of our presentation](https://drive.google.com/file/d/1qARdzY-jT-Gx0qwtZI6ludoEPjJfqSVQ/view?usp=sharing).

* Here are our [presentation slides](https://docs.google.com/presentation/d/1ZYRTfo9rsUTL6RA_iK81w5X1f4Ef6jb8YWHSNIDyfyc/edit#slide=id.g23e1f71fd94_0_32).

<br>

***

<br>

### References and Sources

Basic Raymarching and diffuse shading:
 - https://michaelwalczyk.com/blog-ray-marching.html

SDF Formulas:
 - https://iquilezles.org/articles/distfunctions

Random Noise:
 - https://forum.unity.com/threads/generate-random-float-between-0-and-1-in-shader.610810/

Transforms:
 - https://jamie-wong.com/2016/07/15/ray-marching-signed-distance-functions/
 - https://www.shadertoy.com/view/4tcGDr

Volume:
 - https://docs.unity3d.com/ScriptReference/AudioSource.html

Spectrum Analyzer:
 - https://xr.berkeley.edu/decal/homework/hw2/ 

Music:
 - https://www.youtube.com/watch?v=txXoCP8xngQ&pp=ygUPd2lpIG11c2ljIHJlbWl4
 - https://www.youtube.com/watch?v=vUJASABxV34&pp=ygUPd2lpIG11c2ljIHJlbWl4
 - https://www.youtube.com/watch?v=yyjUmv1gJEg&pp=ygUOd2lpIHNob3AgdGhlbWU%3D
 - https://www.youtube.com/watch?v=-_RBJvfoszk&pp=ygUUc21hc2ggbW91dGggYWxsIHN0YXI%3D
 - https://www.youtube.com/watch?v=YoWEMeJMP5U&pp=ygUObGVnZW5kIG9mIGxpbms%3D

### Member Contributions

#### Ben Cuan
Created scripts for Unity for UI and to connect with ShaderLab to pass parameters. Updated basic raymarching code to work with arbitrary SDFs. Added camera and object rotation. 

#### Ben Lin
Implemented the SDFs for octahedron and torus. Also implemented finding the normal for shading, and diffuse shading.

#### Joel Rodiel-Lucero
Worked on infinite terrain raymarching. Implemented Blinn-Phong shading. Implemented radial gradiant background visualization. Implemented the SDF for Solid Angle.

#### Ishaan Dham
Implemented morphing of different shapes. Worked on infinite sphere raymarching shader. Contributed spectrogram code.


