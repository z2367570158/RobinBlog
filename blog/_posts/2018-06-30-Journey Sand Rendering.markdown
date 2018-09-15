---
layout:     post
title:      " Journey Sand Rendering"
subtitle:   "OMG those shining sands"
date:       2018-06-30 07:30:13
author:     "Robin 升升"
header-img: "img/in-post/post-bg-2018-06-30-Journey Sand Rendering.png"
tags:
     - Portfolio
---

> Journey is always one of my favorite games.

<img src="http://ssylva.com/img/in-post/JourneyBackground.png" width="1000" height="750" />

## Video

<iframe width="560" height="315" src="https://www.youtube.com/embed/o0CjwTAfOUo?rel=0" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

## Mesh

I'm using a procedural generated terrain using perlin noise and vertex control. [Link to my another article](http://ssylva.com/2018/04/15/Terrain-Procedural-Generation/). it's just some hills with no detail.


## Diffuse Color

According to [Journey's GDC Talk](https://www.youtube.com/watch?v=wt2yYnBRD3U), they are using a function called **OrenNayar**.

<img src="http://ssylva.com/img/in-post/JourneyDiffuseFunction.png" width="1000" height="750" />
<img src="http://ssylva.com/img/in-post/JourneyDiffuseModel.png" width="1000" height="750" />
<img src="http://ssylva.com/img/in-post/JourneyDiffuse.png" width="1000" height="750" />

## Normal map

Google Sand texture, get two sand texture in macro view. Modify them into two different direction.

<img src="http://ssylva.com/img/in-post/JourneyNormal1.png" width="1000" height="750" />
<img src="http://ssylva.com/img/in-post/JourneyNormal2.png" width="1000" height="750" />

Google sand texture, get one sand texture in micro view.

<img src="http://ssylva.com/img/in-post/JourneyNormal4.png" width="1000" height="750" />

By calculation normal vector, we can distribute two macro normal map on our terrain.

<img src="http://ssylva.com/img/in-post/JourneyNormalDistribution.png" width="1000" height="750" />
<img src="http://ssylva.com/img/in-post/JourneyNormalDistributionReal.png" width="1000" height="750" />


## Specular

For Specular reflection, I use Blinn model. I create 3 different layers of specular reflection and mix them to make the terrain smoother.

<img src="http://ssylva.com/img/in-post/JourneySpecular1.png" width="1000" height="750" />
<img src="http://ssylva.com/img/in-post/JourneySpecular2.png" width="1000" height="750" />
<img src="http://ssylva.com/img/in-post/JourneySpecular3.png" width="1000" height="750" />

 For more detail and make it more grainy, I add a noise map for reflection.

<img src="http://ssylva.com/img/in-post/JourneySpecularDetail.png" width="1000" height="750" />


## Post-Processing

I use Unity post-processing stack. Using bloom and Color grading to make the visual more Journey-like.

<img src="http://ssylva.com/img/in-post/JourneyPostprocessingOriginal.png" width="1000" height="750" />
<img src="http://ssylva.com/img/in-post/JourneyPostprocessing.png" width="1000" height="750" />


## Final

<img src="http://ssylva.com/img/in-post/JourneyFinal.png" width="1000" height="750" />

