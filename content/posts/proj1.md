---
title: "Project 1: Rasterizer"
date: 2023-02-12
# weight: 1
# aliases: ["/first"]
tags: ["first"]
author: ["Ben Cuan", "Ben Lin"]
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
# description: "Rasterizer"
canonicalURL: "https://canonical.url/to/page"
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
    image: "/foo.jpg" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
---

# Project 1
![test](/proj1/foo.jpg)



## Overview
In this project, we created a simple rasterization engine to draw basic lines, triangles, and shapes defined in SVG format, and apply color and texture to them. 

In addition, we implemented multiple sampling methods, both for changing texture filtering on pixels and on mipmap levels.

Over the course of this project, we learned about how to apply the formulas we learned in class to actually generate images and render them to the screen based on a set of coordinates and instructions.


## Section I: Rasterization


### Task 1: Drawing Triangles

Raterization is a technique to draw 3D objects onto a 2D screen. At its most basic, rasterizing triangles works by sampling from every point in the object space corresponding to a pixel on the screen, and checking to see if that point is inside of the triangle. If it is inside the triangle, then the pixel should be colored the color of the triangle, otherwise the pixel is not colored. 

Our algorithm works by first defining the bounding box of the triangle using the min and max of the three vertices along the x and y coordinates (or the edge of the display if the triangle is cut off). Then we check every pixel within that space for if it is inside the triangle. Trivially, our algorithm is no worse than one that checks each sample within the bounding box since it is exactly this algorithm.

To determine whether the point is actually inside of the triangle, we use the three-line test using the same method as described in lecture. Essentially, this works by treating each line as the boundary of a half-plane, where the half-plane includese the area within the triangle. If a point is inside all three half-planes, then it must be inside of the triangle.

Here is the condition to check all three lines:
```cpp
-(sx - x0) * (y1 - y0) + (sy - y0) * (x1 - x0) >= 0 &&
-(sx - x1) * (y2 - y1) + (sy - y1) * (x2 - x1) >= 0 &&
-(sx - x2) * (y0 - y2) + (sy - y2) * (x0 - x2) >= 0
```

One issue is that this condition assumes that the points (labelled 0, 1, 2) are labelled in a clockwise direction, where 1 is clockwise from 0 and so on.

To check if the points are clockwise, we take a point that we know is guaranteed to be within the triangle (namely, the center of the triangle $((x_0 + x_1 + x_2)/3, (y_0+y_1+y_2)/3 )$), and make sure it passes the three-line test. If it fails, then we rearrange the points by swapping $(x_1, y_1)$ with $(x_2, y_2)$ to make them clockwise.

The image below illustrates how triangles are rendered (before antialiasing):
![task1](/proj1/task1.png)

### Task 2: Antialiasing

For supersampling we sampled the image multiple times within each pixel and then averaging the resulting color for the displayed image. Our supersampling technique sampled evenly within the pixel in a grid formation and saved the pixel colors to a sample buffer of size width or image * height of image * supersampling rate. This buffer stored all the samples for a specific pixel in sequence, which allowed for easier math for sampling and rendering.
Supersampling is useful as an antialiasing technique because smooths out edges by blending the sharp cutoffs. For example, without supersampling, a triangle edge may dip inbetween the sample points of neighboring pixels leading to jaggies, supersampling allows that edge to be captured in some of the samples, leading to a smoother transition between being inside and outside of the shape. 
Our rasterization pipeline changed to sample multiple times according to the supersampling rate and then also render pixels by average the colors for the multiple samples within that pixel. Additionally because liens and points were not supersampled, the fill_pixel method was changed to fill all the samples within a pixel to the corresponding color during supersampling. 
This process allowed us to antialias our triangles by having a smoother edge. As shown in the image, instead of a sharp cutoff, where the tail end of the triangle forms some small islands of pixels, the supersampled images have a smooth transition into lighter shades of red to display the edge of the triangle.



Walk through your supersampling algorithm and data structures. Why is supersampling useful? What modifications did you make to the rasterization pipeline in the process? Explain how you used supersampling to antialias your triangles.
Show png screenshots of basic/test4.svg with the default viewing parameters and sample rates 1, 4, and 16 to compare them side-by-side. Position the pixel inspector over an area that showcases the effect dramatically; for example, a very skinny triangle corner. Explain why these results are observed.
Extra credit: If you implemented alternative antialiasing methods, describe them and include comparison pictures demonstrating the difference between your method and grid-based supersampling.

### Task 3 (10 pts)

Create an updated version of svg/transforms/robot.svg with cubeman doing something more interesting, like waving or running. Feel free to change his colors or proportions to suit your creativity. Save your svg file as my_robot.svg in your docs/ directory and show a png screenshot of your rendered drawing in your write-up. Explain what you were trying to do with cubeman in words.

## Section II: Sampling

### Task 4 (10 pts)

Explain barycentric coordinates in your own words and use an image to aid you in your explanation. One idea is to use a svg file that plots a single triangle with one red, one green, and one blue vertex, which should produce a smoothly blended color triangle.
Show a png screenshot of svg/basic/test7.svg with default viewing parameters and sample rate 1. If you make any additional images with color gradients, include them.

### Task 5 (15 pts)

Explain pixel sampling in your own words and describe how you implemented it to perform texture mapping. Briefly discuss the two different pixel sampling methods, nearest and bilinear.
Check out the svg files in the svg/texmap/ directory. Use the pixel inspector to find a good example of where bilinear sampling clearly defeats nearest sampling. Show and compare four png screenshots using nearest sampling at 1 sample per pixel, nearest sampling at 16 samples per pixel, bilinear sampling at 1 sample per pixel, and bilinear sampling at 16 samples per pixel.
Comment on the relative differences. Discuss when there will be a large difference between the two methods and why.

### Task 6 (25 pts)

Explain level sampling in your own words and describe how you implemented it for texture mapping.
You can now adjust your sampling technique by selecting pixel sampling, level sampling, or the number of samples per pixel. Describe the tradeoffs between speed, memory usage, and antialiasing power between the three various techniques.
Using a png file you find yourself, show us four versions of the image, using the combinations of L_ZERO and P_NEAREST, L_ZERO and P_LINEAR, L_NEAREST and P_NEAREST, as well as L_NEAREST and P_LINEAR.
    To use your own png, make a copy of one of the existing svg files in svg/texmap/ (or create your own modelled after one of the provided svg files). Then, near the top of the file, change the texture filename to point to your own png. From there, you can run ./draw and pass in that svg file to render it and then save a screenshot of your results.
    Note: Choose a png that showcases the different sampling effects well. You may also want to zoom in/out, use the pixel inspector, etc. to demonstrate the differences.
Extra credit: If you implemented any extra filtering methods, describe them and show comparisons between your results with the other above methods.






