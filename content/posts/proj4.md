---
title: "Project 4: ClothSim"
date: 2023-04-10
# weight: 1
# aliases: ["/first"]
# tags: ["first"]
author: ["Ben Cuan", "Ben Lin"]
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: true
description: https://github.com/cal-cs184-student/proj-webpage-bens || https://github.com/cal-cs184-student/p4-clothsim-sp23-bens5
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
    image: "/proj3/walle2.png" # image path/url
    alt: "proj4 cover" # alt text
    # caption: "asdfasdasfd" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
---


## Overview
In this project, we simulated cloth behavior using a mass-spring system.

Essentially, a cloth can be approximated as a uniform grid of point masses all connected using ideal springs. There are three types of springs that give cloths their physical behavior:
 - Structural springs connect point masses with immediately adjacent point masses (top, bottom, left, right).
 - Shearing springs connect point masses with diagonal point masses (top left, top right, bottom left, bottom right).
 - Bending springs connect point masses with adjacent point masses separated by one (2 from top, 2 from bottom, 2 from left, 2 from right). These springs are weaker than the other two spring constraints.

After constructing the cloth using point masses and spring constraints, we can then apply external forces, such as gravity or normal forces due to collisions, to affect how the cloth moves. In this project, we handle collisions with spheres, planes, and with the cloth itself (such that it doesn't overlap itself).

Finally, we implemented shaders, textures, bump maps, and reflections to improve the appearance of the cloth. 

## Part 1: Masses and Springs

In this part, we constructed the basic cloth grid of point masses and spring constraints as described in the introduction. 

Here's what the grid looks like:

```bash
./clothsim -f ../scene/pinned2.json
```
Without shearing constraints:
![p4-screenshot](/proj4/p4-2.png)

With only shearing constraints:
![p4-screenshot](/proj4/p4-3.png)

With all constraints:
![p4-screenshot](/proj4/p4-1.png)


## Part 2: Simulation via Numerical Integration

In this part, we used Verlet integration to update point mass positions based on the total force acting on each point mass (including external forces and spring forces).

For reference, here is the cloth with 4 corners pinned from the scene `pinned4.json` with default parameters:


![p4-screenshot](/proj4/p4-2-1.png)

### Changing the spring constant

The force a spring applies to the two point masses it connects, $a$ and $b$, is calculated using Hooke's law as follows:
$$ F_s = k_s \cdot (||p_a - p_b|| - l) $$

where $k_s$ is the spring constant, $p_a$ and $p_b$ are the positions of the two point masses, and $l$ is the rest length of the spring.

When $k_s$ is low, the spring force is low, making the cloth bouncy and fluid. For example, with $k_s = 100 N/m$, the cloth with 4 pinned corners looks like this right after bouncing up for the first time:

![p4-screenshot](/proj4/p4-2-lowks.png)

At this low $k_s$ setting, the cloth continues to bounce for a long time before settling into its final rest state.

When $k_s$ is high, the high spring forces make the cloth more rigid, so it bounces less and settles more quickly. Here is the same cloth with $k_s = 20000 N/m$ after one bounce:

![p4-screenshot](/proj4/p4-2-highks.png)

### Changing the cloth density

Since $F = ma$, changing the density of the cloth will change its mass (assuming the volume does not change), meaning less force is applied to the cloth.

At a low density ($1 g/cm^2$), the cloth settles into a very shallow bowl shape, and hardly bounces at all while doing so:

![p4-screenshot](/proj4/p4-2-lowdensity.png)

On the other hand, at high density ($100 g/cm^2$), the cloth dips much further down and bounces much more when settling into its final position:

![p4-screenshot](/proj4/p4-2-highdensity.png)


### Changing the cloth damping

The damping term is analogous to energy loss in the system due to forces such as friction. As the damping term increases, the proportion of velocity allowed into the system each timestep decreases.

With no damping, the cloth moves very quickly, with lots of ripples and bounces:
![p4-screenshot](/proj4/p4-2-lowdamping.png)

With maximum damping, the cloth does not bounce at all, and settles immediately into its final rest position:
![p4-screenshot](/proj4/p4-2-highdamping.png)

## Part 3: Collisions with Other Objects

### Changing the spring constant
ks = 500
![p4-3-screenshot](/proj4/p4-3-2.png)

ks = 5000
![p4-3-screenshot](/proj4/p4-3-1.png)

ks = 50000
![p4-3-screenshot](/proj4/p4-3-3.png)

The larger spring constant (ks) causes the cloth to be less tightly draped over the sphere. This is because the higher spring constant provides more force keeping the cloth flat to counterbalance gravity pulling the cloth down around the sphere.

### Image of cloth on plane
![p4-3-screenshot](/proj4/p4-3-4.png)

## Part 4: Self-Collisions

### Cloth falling steps

Initial collision with the plane
![p4-4-screenshot](/proj4/p4-4-1.png)

Cloth folding in on itself
![p4-4-screenshot](/proj4/p4-4-2.png)

Cloth lying at rest
![p4-4-screenshot](/proj4/p4-4-3.png)

### Folded cloth with varying variables

High density
![p4-4-screenshot](/proj4/p4-highdensity.png)

Low density
![p4-4-screenshot](/proj4/p4-lowdensity.png)

With a higher density the cloth seems to spread itself out more when it settles.

High spring constant
![p4-4-screenshot](/proj4/p4-bigKs.png)

Low spring constant
![p4-4-screenshot](/proj4/p4-smallKs.png)

With a high spring constant the cloth does not fold into itself as much, especially while it is initially falling onto the plane. With a low spring constant the cloth folds into itself much more tightly.


## Part 5: Shaders

In this section, we implemented a variety of shaders for the cloth.

A shader program computes the color at every point on objects in the scene based on parameters such as the position, normal direction, lighting, color, and texture map. The vertex and fragment shaders work together: vertex shaders help create material effects by varying the appearance and position at vertices in the object, while fragment shaders fill in the spaces between vertices during rasterization.


### Blinn-Phong

The Blinn-Phong shading model is a method of rendering lighting effects based on material properties. It does this by combining three different types of lighting:
 - Ambient lighting does not depend on the position on the object; it is constant throughout the scene.
 - Diffuse lighting creates the matte appearance of an object by varying intensity based on how far away a point is from the light.
 - Specular lighting creates the shiny reflections of an object by creating a very concentrated version of diffuse lighting through exponentiation of the lighting component, such that large values become extremely bright and small values become negligible.


Here is the Blinn-Phong model with only ambient lighting:


With only diffuse lighting:


With only specular lighting:


### Texture Mapping

Here's our custom texture, the Black Lotus card:
![blacklotus](/proj1/blacklotus_square.png)

This is what the cloth looks like with this texture:


### Bump and Displacement Mapping

Here's the bump-mapped sphere using the brick `texture_3.png`:

Here's the displacement-mapped sphere using the same texture:

Now, here's the same two images with coarseness values `-o 16 -a 16`:


And for `-o 128 -a 128`:


As we can see from the above images, ... TODO


### Mirror Shading
Here's a screenshot of the mirror shading on the cloth and sphere:

Mirror Sphere
![p4-5-screenshot](/proj4/p5-5-sphere.png)

Mirror Cloth
![p4-5-screenshot](/proj4/p5-5-cloth.png)