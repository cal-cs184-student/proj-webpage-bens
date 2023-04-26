---
title: "CS184 Final Project Milestone"
date: 2023-04-25
# weight: 1
# aliases: ["/first"]
# tags: ["first"]
author: ["Ben Cuan", "Ben Lin", "Joel Rodiel-Lucero", "Ishaan Dham"]
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: true
description: Raymarching Milestone
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
    alt: "proj2 cover" # alt text
    # caption: "asdfasdasfd" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
---

# [Milestone] Endless Procedural Landscape with Raymarching
Ben Cuan, Ben Lin, Joel Rodiel-Lucero, Ishaan Dham

## Summary

![A Raymarched scene of Endless Oscillating Spheres dancing to the Mii Channel Trap Remix](/final/forever.png)


So far we have been able to generate a raymarching scene of infinite spheres. Additionally we added music [(mii channel VGC Remix)](https://www.youtube.com/watch?v=txXoCP8xngQ) and have the inner circles pulsate to the beat of the music.

## Preliminary Results

The scene of a moving infinite landscape of spheres is generated using the `distance_estimator` function in `infSpheres.shader`. 

In order to add the pulsating spheres we simply modified `distance_estimator` to correspond with the audio of the music. This was done by first using [Perlin noise](https://en.wikipedia.org/wiki/Perlin_noise) to get random values. Then we scaled these values to modify the raymarching distance estimator using the following formula:

$$ D' = d * a * l + b * l $$
 * $d$ is the original output from the distance to the closest sphere for the current ray,
 * $a$ and $b$ are independent Perlin noise generators,
 * $l$ is the loudness of the sound clip at the current time step (between 0 and 1).

## Progress Reflections

So far, we have been able to meet and exceed our goals for the raymarching portion of our project timeline. We were able to generate basic shapes using the raymarching algorithm, and even figure out how to manipulate the appearance of these spheres dynamically based on some input parameter (volume level of an audio clip) from Unity.

We have also been able to make some progress on landscape generation, but do not currently have a working prototype to demonstrate this. This will be our next major priority for the next several days.

## Updated Work Plan

Given that we have have reached our given tasks in time, we will be following schedule:
### Week 1

- [x] Set up Unity and the GitHub repo to allow for collaborative access to our project code 
- [x] Initialize a basic test scene in Unity and produce a build of a blank project
- [x] Review raymarching literature and create some psueudocode
- [x] Review procedural generation literature and create some psueudocode

### Week 2

- [x] Begin implementation of raymarching algorithm in Unity
    - [x] Render basic shapes (sphere, cube...) using raymarching
- [x] Begin implementation of procedural terrain generation algorithm in Unity
    - [x] Generate a uniform infinite landscape (periodic with simple function)

### Week 3
- [x] Finish raymarching implementation
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
- [ ] New stretch goal: add user-friendly interface to customize sound input
- [ ] New stretch goal: beat detection for further dynamic movement of raymarching