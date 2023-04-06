---
title: "CS184 Final Project Proposal"
date: 2023-04-03
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
    image: "/proj3/walle2.png" # image path/url
    alt: "proj2 cover" # alt text
    # caption: "asdfasdasfd" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
---

# Endless Procedural Landscape with Raymarching
Ben Cuan, Ben Lin, Joel Rodiel-Lucero, Ishaan Dham

In this project, we will implement a Raymarching infinite landscape scene in Unity. In other words, we will extend the ray-tracing algorithms learned in class to subdivide rays over a spherical surface, speeding up rendering and allowing for the rendering of more advanced implicit geometry. 

## Problem Description

Generally speaking, the problem we are trying to solve is enabling the rendering of infinite, procedurally generated shapes in an efficient manner to create asthetically pleasing landscape scenes that can be applied to many applications such as video games or music visualization.

This is challenging because the raytracing algorithms we've learned in class are unable to efficiently render implicitly defined shapes. An added challenge to this is that we will be going beyond the scope of the course in order to implement raymarching.

A large volume of research has already been conducted on raymarching algorithms- specifically sphere-assisted raymarching- so we plan to extend upon pre-existing solutions to suit the specific needs of this project. In addition, we will combine these raymarching solutions with other essential elements of computer graphics such as volumetric lighting, shading, and texture mapping.

## Goals and Deliverables

The primary goal for this project will be to render an infinitely generating landscape using raymarching. As a stretch goal, we want to make this landscape look pretty.

### Primary Objectives

The baseline product we hope to create involves the following main points:
1. Implement a sphere-assisted raymarching algorithm that can be used to render arbitrary implicit shapes.
2. Implement an algorithm to procedurally generate a landscape
3. Combine points (1) and (2) using the Unity engine to render the landscape in a manner accessible to external viewers.
4. Create a sample landscape to demonstrate the capabilities of our implementation, and use this sample landscape to provide a deliverable in the form of a demo video.


### Stretch Goals

If we have additional time, we hope to be able to add the following deliverables to our project:
1. Use texture mapping to add a texture to the landscape
2. Use environmental lighting to light the landscape in realistic manner



## Schedule

### Week 1

- [ ] Set up Unity and the GitHub repo to allow for collaborative access to our project code 
- [ ] Initialize a basic test scene in Unity and produce a build of a blank project
- [ ] Review raymarching literature and create some psueudocode
- [ ] Review procedural generation literature and create some psueudocode

### Week 2

- [ ] Begin implementation of raymarching algorithm in Unity
    - [ ] Render basic shapes (sphere, cube...) using raymarching
- [ ] Begin implementation of procedural terrain generation algorithm in Unity
    - [ ] Generate a uniform infinite landscape (periodic with simple function)

### Week 3
- [ ] Finish raymarching implementation
    - [ ] Extend raymarching to render any shape
- [ ] Finish procedural generation implementation
    - [ ] Allow for adjustment of parameters to create interesting features such as hills
    - [ ] Introduce randomness into scene
- [ ] Combine both raymarching and procedural generation into a single scene

### Week 4
- [ ] Create demo video and presentation
- [ ] Finish writeup
- [ ] Stretch goal: implement texture mapping
- [ ] Stretch goal: implement lighting

## Resources

- NVIDIA's Graphics Codex on Ray marching [link](https://graphicscodex.courses.nvidia.com/app.html?page=_rn_rayMrch)

- Paper on Procedural Terrain Generation Using Ray Marching [link](https://www.diva-portal.org/smash/get/diva2:1059684/FULLTEXT01.pdf)

- Ray marching in Unity [link](https://gist.github.com/javiersalcedopuyo/fac64012fd36082571c07e6b46d24e70)

- Unity Engine [link](https://unity.com/)

