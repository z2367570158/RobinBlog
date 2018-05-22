---
layout:     post
title:      "《Unity Shader入门》 学习笔记"
subtitle:   " \"程序员的外貌协会\""
date:       2018-03-02 07:30:13
author:     "Robin 升升"
header-img: "img/in-post/post-bg-2018-03-02-unity-shader.jpg"
tags:
     - Unity
     - Shader
---

> “程序员的三大浪漫：编译原理，操作系统，图形学”

<p id = "build"></p>

##  第二章 GPU流水线介绍

渲染流程：应用阶段，几何阶段，光栅化阶段。

    应用阶段负责准备数据，输出输出渲染所需几何数据，即渲染图元。

    加载数据到显存————设置渲染状态————调用Draw Call.

几何阶段在GPU上计算绘制渲染图源的相关操作细节。

    顶点数据————着色器————裁剪————屏幕映射

光栅化阶段即负责将数据产生在屏幕上。  

    三角形设置————三角形遍历————片元着色器————屏幕图像


常见着色器语言 HLSL-DirectX、GLSL-OpenGL、Cg-NVIDIA。

<br/>
Draw Call过多会影响效率，要用批处理(Batching)缓解，即打包多个需要处理的网格。



<br/>
##  第三章 Unity Shader基础

1. Unity中，需要Material和Shader同时使用才能最终呈现出效果。


2. Unity中大致有四种Shader：Standard Surface Shader、Unlit Shader、Image Effect Shader、Compute Shader。

    Standard Surface Shader是包含了标准光照的模板。

    Unlit Shader则是不包含光装的模板。

    Image Effect Shader 各种屏幕后期处理效果模板。

    Compute Shader大概是用来做GPU计算的一类模板吧，并不常用。

3. Unity Shader基础结构

```
Shader "ShaderName"{

    properties{
        //属性
        Name("display name", PropertyType) = Value
        _Int ("Int", Int) = 2
        _Float ("Float", Float) = 1.5
        _Range ("Range" Range(0.0, 5.0)) = 3.0
        _Color ("Color" Color) = (0,0,0,1)
        _Vector ("Vector" Vector) = (1,2,3,4) //这里vector是个四维向量是因为空间变换中齐次向量用的比较多，w分量为1则说明向量是点，w分量为0则说明向量是方向。
        _2D ("2D", 2D) = "" {}
        _Cube ("Cube" Cube) = "white" {}
        _3D ("3D", 3D) = "black" {}
    }
    SubShader{
        //子着色器

        //[Tags]
        eg. Tags { "Queue" = "Transparent" "RenderType" = "Opaque" "DisableBatching" = "True" }

        //[RenderSetup]
        Cull Back

        Pass{
            //[Name]
            Name "FIRSTPASS"

            //[Tags]和SubShader里面的Tag内容值不一样
            eg.Tags { "LightMode" = "ForwardBase" "RequireOptions" = "SoftVegetation" }

            //[RenderSetup]和SubShader一样

            --对于顶点/片元着色器的CG/HLSL代码一般是放在Pass中
            CGPROGRAM           CG/HLSL代码
            
             #programa vertex vert
            #programa fragment frag
            ENDCG

            GLSLPROGRAM         GLSL代码
            
            ENDGLSL
        }
        Pass{
            ···
        }
        ···
    }
    SubShader{
        //子着色器
    }

    ···

    Fallback "VertexLit"
}
```

4. 几种着色器: 表面着色器(surface shaders)，顶点/片元着色器(vertex and fragment shaders)，固定函数着色器(fixed function shaders)

    表面着色器需要写在SubShader里面

    顶点片元着色器则要写在Pass里面

    固定函数着色器大概我这辈子没机会用到了吧

<br/>
##  第四章 数学基础

>"I'm the God of Math!!!"

1. 左手系右手系的旋转时不同正方向的！！！左手系就左手法则旋转，右手系就右手。

2. Unity里用左手系，但是摄像机的观察空间用右手系。（不是很懂）

3. a向量+b向量结果由a到b，a向量-b向量，结果由b到a

4. a向量·b向量=b在a上的投影长度

    妙用：场景中有个NPC的位置是p点，他的前方可以用向量forward表示，玩家现在位于x点，判断玩家在npc的前方还是后方
    (x-p)·forward   >0则在前方，<0则在后方。

5. a向量×b向量=一个与他们正交垂直的向量，大小=丨a丨丨b丨sinƟ。

    妙用：空间内有一个三角形abc,三个顶点分别是A,B,C. 我们可以用AB,AC的叉积获得ABC所在平面的法线向量，与我们视线的向量做点积即可知道我们是从三角形的正面还是背面在看它了。

6. 标准正交矩阵的逆矩阵等于其转置矩阵

    妙用：当用标准正交基矢量来构建矩阵的时候，旋转是不会改变其标准正交性的，即我们可以直接求其转置矩阵来得到逆矩阵。

7. 由于行矩阵（左乘）和列矩阵（右乘）与另外一矩阵相乘结果是不同的，所以将向量变化为矩阵时需要特别小心。

    Unity里面同意采用右乘，也就是将向量变为列矩阵进行计算。
    一向量v，和三个矩阵ABC右乘CBAv=左乘vA(T)B(T)C(T) T为转置

8. （未完待续）


