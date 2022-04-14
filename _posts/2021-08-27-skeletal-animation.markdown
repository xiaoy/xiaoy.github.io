---
layout:     post
title:      "骨骼动画"
subtitle:   "" 
date:   2021-08-27
author: "武龙飞"
tags:
    - 游戏编程 
    - 2021
---

## 简介
光通过空气传播，遇到物体后根据物体的组成材料发生反射和折射，反射的光达到人眼，眼睛的感光细胞将光信号转换神经信号，经过神经传递到大脑，最终成为了我们脑中的一幅图。当物体发生变形(位移，缩放，旋转）时，对应反射光方向和强弱也会发生变化，这就是我们脑海中对应的物体动画。

![ ](/img/in-post/2021-08-27-skeletal-animation/light-eye-brain.jpeg)

计算机图形学的核心就是通过三角形面构建虚拟世界中物件的形状，形状相同，但不同的形状有不同的物理属性，所以引入了材质的概念，然后加入虚拟的光照，通过相应的光照模型算法，渲染出一张贴图，贴图发送到设备屏幕，屏幕上有相应的发光体，发光体发射的光进入我们的眼睛，然后就在大脑中看到了渲染贴图。

我们要介绍的骨骼动画，主要针对的是骨骼驱动的所有物件。

## 历史
当人眼接收图像的时间小于1/16秒的时候，人脑就会产生连续的动画。对于游戏中的图形渲染，通常根据游戏类型不同，帧率通常为30fps，或者60fps，最近这些年热门的VR游戏，帧率要达到90fps，大脑才不会产生眩晕。

刚开始的游戏开发，大家通过大量绘制物体在不同时间的形态贴图，然后按照帧率显示在屏幕上。这样的好处是美术绘制人员需要耗费大量的人力，同时扩展性非常差，比如一个人物角色可以沿任何方向移动，就这一个移动动画就需要绘制各个方向不同状态的贴图。这很不经济，也不高效。

后面提出骨骼动画的概念，最初提出此概念的人也许通过人穿着衣服，做不通的动作就会带着衣服做同样的动作。这样来说，美术同学只需要绘制一张“衣服”的贴图，动作设计师专心通过骨骼制作动画即可。

## 骨骼动画原理
3d模型是通过三角形面组合而成，通过相应图形学算法我们只能得到一个静态的贴图。
![ ](/img/in-post/2021-08-27-skeletal-animation/3d-mesh.jpeg)

要想得到动画，就需要按照特定频率将三角形面上的顶点进行变换。

这时引入了骨骼动画，每一帧的图像通过顶点和骨骼节点所在的位置关系进行运算。

所以这里我们需要知道顶点变换矩阵。

顶点变换矩阵来自己骨骼节点，骨骼节点变换对应动画，动画是连续的，我们需要通过采样方式来得到动画帧。当动画播放时，根据当前动画所在的时间得到相应变换：`p = a(1- t) + bt`

## 动画文件格式
骨骼是由节点组成，每个动画(animation)的数据结构如下：

```C++
struct AiNode
{
    string name;
    mat4x4 transformation;
    AiNode* parent;
    AiNode* children[];
};
struct AiScene
{
    AiNode rootNode;
    Animation animations[];
};
struct AiNodeAnim
{
    string name;
    vector3d postions[];
    quaternion rotations[];
    vector3d scalings[];
};
struct Animation
{
    double duration;
    double tickPerSecond;
    AiNodeAnim channels[];
};
```

### mesh 存储
Assimp mesh数据结构:

```C++
struct AIScene
{
    AIMesh aiMesh[];
};

struct AIMesh
{
    Vector3d vertex[];
    AIBone aiBone[];
};

struct AIBone
{
    string name;
    AIVertexWeight aiVertexWeight[];
    Matrix offsetMatrix;
};

struct AIVertexWeight
{
    uint vertexId;
    float weight;
};
```
mesh由顶点组成，每个顶点的位置通过骨骼来计算。一个顶点由多个骨骼决定，最终的位置:
```glsl
#version 430 core
layout(location = 0) in vec3 pos;
layout(location = 1) in vec3 normal;
layout(location = 2) in vec2 tex;
layout(location = 5) in ivec4 boneIds;
layout(location = 6) in vec4 weights;

uniform mat4 projection;
uniform mat4 view;
uniform mat4 model;

const int MAX_BONES = 100;
const int MAX_BONES_INFLUENCE = 4;
uniform mat4 finalBoneMatrices[MAX_BONES];

out vec2 TexCoords;

void main()
{
    vec4 totalPosition = vec4(0.0f);
    for(int i = 0; i < MAX_BONES_INFLUENCE; ++i)
    {
        int boneIndex = boneIds[i];
        if(boneIndex >= MAX_BONES)
        {
            totalPosition = vec4(pos, 1.0f);
            break;
        }
        vec4 localPostion = finalBoneMatrix[bondIndex] * wights[i];
        totalPosition += localPosition *  vec4(pos, 1.0f);
    }
    mat4 viewModel = view * model;
    gl_Position = projection * viewModel * totalPosition;
    Texcoord = tex;
}
```