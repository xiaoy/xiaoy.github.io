---
layout:     post
title:      "如何学习图形学"
subtitle:   "" 
date:   2020-01-10
author: "武龙飞"
tags:
    - 图形学
    - 2020
---
## 前言
这里说的图形学主要指的是游戏开发中的图形学。游戏引擎的核心功能包括但不限于：图形渲染，游戏逻辑，音效，资源管理，打包输出游戏应用。市面上大多数游戏引擎使用C++实现，而游戏渲染使用的代码叫做shader，shader大多语法数借鉴于C语言，因此学习图形学第一步就是打好C/C++基础。

具备了和计算机交流的语言之后，就可以选择一个具体方向来学习和研究了。计算机图形游戏领域具体要做的事情就是将现在的资源按照一定的规则发送到GPU上，GPU渲染完成后，放到对应的内存，最后显示在屏幕上。如何将这些资源发送到GPU上，每家GPU有自己的底层驱动程序来实现，抛出相应的接口供大家调用。因为有不同的操作系统，所以GPU驱动会为相应的操作系统发布对应的驱动。操作系统开发商会开发自己对GPU驱动接口的封装。微软研发了自己的Directx来和GPU通信，但仅限于Windows操作系统。所以就有了跨平台的接口：**OpenGL**，从学习角度来看Opengl是最好的选择。

基本熟练掌握OpenGL后，就可以启动我们伟大的征程，游戏引擎开发。说的是图形学学习，咋变游戏引擎开发了，因为游戏图形学就是为游戏引擎服务，同时通过游戏引擎我们可以很好的验证自己的学习成果，再者学习了解游戏引擎全局，对于工作来说如虎添翼。

## 兴趣
在自己的学习工作上，兴趣是最好的老师。对相应的领域产生兴趣，才花更多的时间精力去研究。花费的时间精力又会给生活工作带来正向反馈。这样就形成了一个积极的正向激励机制，最终在这个领域创造价值，取得成果，成为专家。

是否对游戏图形学感兴趣，可以通过下面列出的历练来确定自己是否喜欢：

* 入门级别，通过查找[shadertoy](shadertoy.com)方面的资料，简单实现一些图形效果
* 通过[ray tracing in one weekend](http://in1weekend.blogspot.com/2016/01/ray-tracing-in-one-weekend.html)这本书实现光线追踪
* 通过[Learn Opengl](https://learnopengl.com/)前序章节的学习了解渲染引擎如何起步

通过这些历练，对图形学，游戏引擎有了大概了解，从这些编码活动知道了自己是否对这个领域有兴趣。

## 入门
到了这一步，算是确定了自己的职业方向，在学习Learn Opengl 之前，先实现软光栅化，通过教程[tinyrenderer](https://github.com/ssloy/tinyrenderer/wiki/Lesson-1:-Bresenham%E2%80%99s-Line-Drawing-Algorithm)，完全搞明白渲染管线中GPU是如何工作的。

接下来在将所有Learn Opengl 的内容看懂，学透。*基于物理渲染*的可以往后放一下，因为涉及到物理和高等数学。然后跟随Learn Opengl教程实现一个2d引擎。2d引擎完成后，开始通过自己的引擎实现简单的游戏，比如打飞机，马里奥这些。

在学习 Learn Opengl教程中，发现需要学习很多的理论方面的知识，这个阶段学习和了解线性代数，投影几何，几何代数，并将这些基础知识牢牢掌握，因为很多渲染错误，都要依靠这些基础知识来调试修复bug。

所以很多大牛建议，开始学习时不要使用任何商业引擎或框架，因为里面有太多的魔法("magic")干扰你理解背后运行的原理。商业引擎需要的时候再去研究，懂得了底层原理，对于商业引擎的取舍以及历史原因就会更加包容。一开始就使用商业引擎容易迷失在盒子里，而看不到整个游戏引擎的全貌。

## 练习
做一个光线追踪(path-tracer)，也可以说是软光栅化(software rasterizer)。使用 Matt Pharr's的书《Physically based ray tracing more or less》，完成这个项目。

做一个生产引擎("production engine")，如果基本完成的话，可以发布到外面。尽你最大努力，抽象好平台相关的接口。最大化使用多线程和基于图的抽象。

有一个引擎原型。选择一个API(最好选择Vulkan，因为我相信Vulkan的说明文档相比D3D12的文档对于初学者更加清晰)。不要因为尝试封装所有代码而陷入泥潭，不用使它变的完美，只要能运行即可。在这里就是你做大多数实验的地方。


学习了解压缩和抗锯齿。写一个[BCn](http://www.reedbeta.com/blog/understanding-bcn-texture-compression-formats/)解码器。阅读视频解码器如何运行的原理。研究并实验各种AA技术(MSAA，MLAA，FXAA，TXAA，etc)。

学习随时要使用的工具。Core dumps，GPU dumps，frame dumps，visualizers，以及其他。当需要时编写自己的工具。这里强烈安利是**RenderDoc**，对于游戏图形开发学习非常有用。
## 实践
当你正式加入游戏产业，一直要尝试涉猎美术的工作(光照(lighting)，贴图(texture)，模型(modeler)，特效(vfx)，技术美术(technical artist)，其他)。观察他们的工作流程帮助你理解内容管线是如何组织起来的。你同时也能想到改善流程的点子。

## 方法
问大量的问题。图形学非常广，无论你学习多少方向，总是人外有人，天外有天。专家通常都非常乐于帮助解答问题并且提供建议。

不要因为要花大量的时间而被吓住。每次选择技术/方法/算法中的一个。慢不是问题，持续改善精进相比一次性全部搞定更加科学合理有效。

当你不知道一些东西的时候要迅速承认否则你将错失学习的机会。拥抱事实，你工作的领域有太多的东西去学习和探索。

同时，不要认为你在线阅读到的东西是绝对真理。通常所有事物总是在运动变化中(硬件，驱动，接口，还有我们自己对最好的方式去做事情的理解)。

不要陷入A技术和B技术的辩论中(例如前向渲染(forwrad)和延迟渲染(deferred)，forward+ 和 tiled defferred， 以及其他)。学习这些技术的核心原理以及取舍。实现的复杂度也是作为取舍的一项判断标准。

## 高级
到了这个阶段，想进阶看懂论文，需要的数学理论知识为：四元数，对偶四元数，多元微积分，信号理论，傅里叶变换，谐波分析，离散数学。

高级阶段性能优化是非常重要的领域，学习和了解数据结构，尤其是[加速结构](https://www.scratchapixel.com/lessons/advanced-rendering/introduction-acceleration-structure)(acceleration structures)，并且理解I/D-cache命中率的重要性，分支指令对性能的影响，以及对这些问题引起的延迟有所意识。

要想让自己的知识更加完善，以及博采众长，学习其他引擎，尤其是非通用引擎。例如看[@MyNameIsMJP](https://twitter.com/MyNameIsMJP)或[@MichalDrobot](https://twitter.com/MichalDrobot)的演讲。来自[@mirror2mask](https://twitter.com/mirror2mask)关于Bungie's 引擎的演讲是现代引擎结构组织的非常棒的入门。这里面一些经验以及高级技巧通过每年的GDC分享，就能获得最新的技术实践。

最后如果想让自己的引擎非常的前沿，作为工程师，将最新的技术工程化，开始阅读论文并且尝试实现是最好的选择。我首先推荐的是 Unreal BRDF 论文。理解里面用的导数，以及尝试重新生成结果。

## 参考

* [niepoints 发表在twitter上言论](https://twitter.com/m_ninepoints/status/1215429886715629569)
