---
layout:     post
title:      " Unity Rendering Research"
subtitle:   "If something looks correct, it is."
date:       2018-08-31 07:30:13
author:     "Robin 升升"
header-img: "img/in-post/post-bg-2018-08-31-Unity Rendering Research.jpg"
tags:
     - Portfolio
---

## Overview

With Unity engine, I started a 3 months learning of rendering, including linear algebra, computer graphics, rendring pipeline, shader writting in Unity and most important, the ideas and methods to implement or try to implement something you want in you screen.

## Math

There are too many things about math in the rendering pipeline. What I think is more important and interesting is the process to calculate something in the gameworld to you screen.

**Model space -> World space -> View Space -> Projection(Homogeneous clip space) -> Screen Mapping**

Believe me math is really interesting if you can understand it. The whole math process can be found in my another article:[link].

## Lighting models

Classic lighting model by Bui Tuong Phong in 1975, defined that light contains 4 parts: Emissive, Specular, Diffuse, Ambient.[Link](https://en.wikipedia.org/wiki/Phong_reflection_model)
So in Unity, Ambient part is based on the environment(often provided by Unity). Emissive part is based object itself. We generally concentrate more on Diffuse and Specular part.

**Diffuse**, the ratio of reflection of the diffuse term of incoming light([Lambertian reflectance](https://en.wikipedia.org/wiki/Lambertian_reflectance)).
<img src="http://ssylva.com/img/in-post/DiffuseFormula.jpg" width="1000" height="750" />
<img src="http://ssylva.com/img/in-post/DiffuseModel.jpg" width="1000" height="750" />
1. Rendering in vertex shader.
2. Rendering in fragment shader.
3. Mapping shadow from [-1,1] to [0,1].

**Specular**, specular reflection constant, the ratio of reflection of the specular term of incoming light,
<img src="http://ssylva.com/img/in-post/SpecularFormula.jpg" width="1000" height="750" />
<img src="http://ssylva.com/img/in-post/SpecularModel.jpg" width="1000" height="750" />
1. Rendering in vertex shader.
2. Rendering in fragment shader.
3. Another specular model called blinn, it uses a different formula to calculate specular and have more realistic visual.


## Texture lighting

After we have our basic lighting model, we can go further with our texture mapping. Unity has already done a lot for us. We only need to calculate size and offset.
<img src="http://ssylva.com/img/in-post/TextureCode.jpg" width="1000" height="750" />

As something on screen after 2000, our texture must have bump maps!!! So let's do normal map for our lighting model. There are two ways to calculate the bump map, in tangent space or in world space. In my opinion, in world space is much better because the calculation is in fragment shader so later we can apply more things on it such as cubemap environment.
<img src="http://ssylva.com/img/in-post/TextureBase.jpg" width="1000" height="750" />

## Transparent Rendering
Transparent rendering is getting a little bit harder since the rendering order is quite confussing. The key idea are z-test and zwrite. After rendering a non-transparent object, it will also blend/rewrite the color buffer and z buffer. This will trigger tremendous confusing conditions like rendering a transparent object and a non-transparent object. 

The final idea is: **Rendering all the non-transparent objects first, then keep z-test enabled but disable z-write so we can rendering transparent object from far to close.**

**Basic transparent rendering**
<img src="http://ssylva.com/img/in-post/1SideTransparent.jpg" width="1000" height="750" />
 1. Transparent by alpha cutoff.
 2. Transparent by alpha blend.

**More complicated**
<img src="http://ssylva.com/img/in-post/TransparentCorrect.jpg" width="1000" height="750" />
1. Applying Transparent shader to object with more complicated shape.
2. Add a new pass when rendering by simply enabling z-write but disabling color output. Then the second pass do tranparent rendering so we can have correct result. 

**Two sides transparent**
<img src="http://ssylva.com/img/in-post/2SideTransparent.jpg" width="1000" height="750" />
1. Add a pass to rendering back side for alpha blend.
2. Add a pass to rendering back side for alpha cutoff.

## Light and Shadow

<!-- 
> The shadow? the shadow!? the shadow!
As you may notice that we didn't talk about any environment so far (it's such a huge topic). 

Let's start with **rendering path**. In Unity, there are generally 2 different types of rendering type: **forward** and **deferred**.
 -->
**Forward rendering**: Z-test -> Color calculation -> Update buffer. 

Foreward rendering is very easy to understand. If something is visible, then render it. But if something is within the range of many lights, then unity will render it using multiple pass in shader. So if we have N objects, M lights, then we'll need N*M pass to render them. Very very expensive.

**Deferred rendering**: Calculate G buffer -> render G buffer.

Deferred rendering is the way to solve the problem we mentioned before. It uses two passes to calculate. First one doesn't calculate any light or shadow or color. It will only calculate things we can see on our screen, then push them (with information we need like pos, normal...) to a buffer called G buffer. Second pass then, we do all the rendering calculation.

**For lights in Forward rendering**

<img src="http://ssylva.com/img/in-post/ForwardLightsScene.jpg" width="1000" height="750" />
<img src="http://ssylva.com/img/in-post/ForwardLights.jpg" width="1000" height="750" />

**For shadow in Forward rendering**

<img src="http://ssylva.com/img/in-post/ForwardShadowScene.jpg" width="1000" height="750" />
<img src="http://ssylva.com/img/in-post/ForwardShadow.jpg" width="1000" height="750" />


## Cubemap Texture.

We can use cubemap as a property in shader to create more realistc visual.

By applying reflection, we can have our "glass" pot.
<img src="http://ssylva.com/img/in-post/Reflection.jpg" width="1000" height="750" />


By applying refrection, we can have our "water" pot.
<img src="http://ssylva.com/img/in-post/Refrection.jpg" width="1000" height="750" />


By applying [fresnel reflection](https://en.wikipedia.org/wiki/Fresnel_equations), we can implement an important part of PBR.
<img src="http://ssylva.com/img/in-post/Fresnel.jpg" width="1000" height="750" />



## Post Processing.

The idea of post processing is after rendering everything into one image and before we show it on the screen, we can have multiple ways to improve or modify it.

**Visual improvement**
<img src="http://ssylva.com/img/in-post/PostProcessing.jpg" width="1000" height="750" />
1. The original image.
2. Drawing the outline by Convolution with Sobel Kernel .
3. Gaussian Blur by 5*5 Gaussian kernel.
4. Bloom by mix highlighten blur part and original image.

**Motion Blur by mixing accumulation buffer**
<img src="http://ssylva.com/img/in-post/MotionBlur.gif" width="1000" height="750" />

**Globel Fog**
<img src="http://ssylva.com/img/in-post/fog.jpg" width="1000" height="750" />

## 2D Rendering

Impressed by games like Okami, Naruto and Street Fighter, 2D Rendering is always my personal interest. The basic 2D rendering is **multi-ramps**. By applying the diffuse color into several ramp so we can have anime-like visual.

<img src="http://ssylva.com/img/in-post/MultiRamps.jpg" width="1000" height="750" />

If we consider those ramps color into something separately, for example, strokes in Sketch. Then we'll get something more stylish.

<img src="http://ssylva.com/img/in-post/Sketch.jpg" width="1000" height="750" />


## Using noise

By noise, we can have more dynamic effects.

**Dissolve effect by applying a noise map alpha from low to high**
<img src="http://ssylva.com/img/in-post/Dissolve.gif" width="1000" height="750" />

**Water effect by shifting normal map.(Also with fresnel reflection)**
<img src="http://ssylva.com/img/in-post/Water.gif" width="1000" height="750" />