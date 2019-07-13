---
layout:     post
title:      "三角形渲染旅程"
subtitle:   "" 
date:   2019-07-06
author: "武龙飞"
tags:
    - 渲染管线
---

## 三角形渲染全貌
1. cpu将三角形从硬盘加载到内存
2. cpu将内存数据拷贝到gpu显存
3. gpu按照渲染管线流程将三角渲染完放到目标framebuffer
4. 显示器按照刷新频率从交换区读取三角形信息，显示到屏幕

![](/img/in-post/2019-07-06-triangle-life/all.png)

## 三角形制作
1. 使用3d制作工具创建3d模型
2. 三角形每个顶点有自己的属性

![](/img/in-post/2019-07-06-triangle-life/vertex.png)

## 导出和导入
1. 使用3d软件将3d模型导出对应的模型文件 (fbx,3ds,dae,abc,ply,obj,x3d,stl)
2. 再将3d模型导入游戏引擎，每个引擎有自己支持的格式

## 渲染
### 游戏引擎
游戏引擎需要处理的事情

![](/img/in-post/2019-07-06-triangle-life/game_engine.png)

#### 创建网格，CPU端
1. 所有的API同样的流程
2. 为顶点创建缓存
3. 为索引创建缓存

![](/img/in-post/2019-07-06-triangle-life/cpu_mesh.png)

#### 创建网格，GPU端
将CPU端的mesh通过硬件抽象层发送到GPU端，本质上是将CPU缓存发送到GPU显存

![](/img/in-post/2019-07-06-triangle-life/cpu_to_gpu_mesh.png)

#### 可见性测试
避免显卡多多余的工作，使用可见性测试，将看不见的物件剔除掉，使用以下两种方式：

1. 视椎体剔除 (Frustu culling)
2. 遮挡剔除 (Occlusion culling)

![](/img/in-post/2019-07-06-triangle-life/visibility_testing.png)

#### 图形驱动全貌

![](/img/in-post/2019-07-06-triangle-life/graphics_driver.png)

#### 图形接口

![](/img/in-post/2019-07-06-triangle-life/graphics_api.png)

#### 用户模式驱动

![](/img/in-post/2019-07-06-triangle-life/graphics_user_mode.png)

#### 用户模式-CPU端PM4结构

![](/img/in-post/2019-07-06-triangle-life/pm4_struct.png)

#### 操作系统层的作用

![](/img/in-post/2019-07-06-triangle-life/operating_system.png)

#### CPU 和 GPU 连接桥

![](/img/in-post/2019-07-06-triangle-life/bridge.png)

## GPU 端介绍
#### 命令处理器

![](/img/in-post/2019-07-06-triangle-life/command_processor.png)

#### 从 packet 解析出三角形信息

![](/img/in-post/2019-07-06-triangle-life/triangle_info.png)

#### GPU全流程

![](/img/in-post/2019-07-06-triangle-life/all_gpu.png)

#### GPU-几何处理器

![](/img/in-post/2019-07-06-triangle-life/gpu_geometry_engine.png)

#### GPU-着色输入处理器

![](/img/in-post/2019-07-06-triangle-life/gpu_shader_processor_input.png)

#### GPU-运算单元

![](/img/in-post/2019-07-06-triangle-life/gpu_compute_unit.png)

#### GPU-着色导出处理器

![](/img/in-post/2019-07-06-triangle-life/gpu_shader_export.png)

#### GPU-基础图员汇编器

![](/img/in-post/2019-07-06-triangle-life/gpu_primitive_assembler.png)

#### 光栅化扫描器

![](/img/in-post/2019-07-06-triangle-life/gpu_scan_converter.png)

#### 颜色和深度处理器

![](/img/in-post/2019-07-06-triangle-life/gpu_color_depth.png)

#### GPU处理完整流程

![](/img/in-post/2019-07-06-triangle-life/gpu_unrolled.png)

#### 几何处理器详细

![](/img/in-post/2019-07-06-triangle-life/gpu_geometry_engine_detailed.png)

#### shader输入处理器（顶点管线）

![](/img/in-post/2019-07-06-triangle-life/gpu_spi_detailed.png)

![](/img/in-post/2019-07-06-triangle-life/gpu_spi_detailed_2.png)

#### 运算单元 （顶点管线）

![](/img/in-post/2019-07-06-triangle-life/gpu_cu.png)

* 每个CU包含四个SIMD运算单元
* SIMD包含64个运算单元（线程），这64个运算单元称作Wavefront
* 每个Wavefront对应的Vector寄存器包含64个浮点存储位
* 每个Wavefront对应的Scalar寄存器包含一个浮点存储位

以下对应执行顶点shader的一个例子：

![](/img/in-post/2019-07-06-triangle-life/gpu_cu_2.png)

因为每个Wavefront执行的指令都一样，遇到分支的时候，检测对应线程上执行的数据如果无效，将舍弃

![](/img/in-post/2019-07-06-triangle-life/gpu_cu_3.png)

下面这张图展示了：最优结果，最坏结果，以及实践平均结果

![](/img/in-post/2019-07-06-triangle-life/gpu_cu_4.png)

#### shader导出处理器（顶点管线）

![](/img/in-post/2019-07-06-triangle-life/gpu_sx.png)

#### 基础汇编器

![](/img/in-post/2019-07-06-triangle-life/gpu_pa.png)

使用一系列优化方法，将不需要渲染的三角形舍弃

![](/img/in-post/2019-07-06-triangle-life/gpu_pa_1.png)

#### 光栅化处理器

![](/img/in-post/2019-07-06-triangle-life/gpu_sc.png)

三角形形状和像素点分布的叠加图，这只是理论上用来说明光栅化原理

![](/img/in-post/2019-07-06-triangle-life/gpu_sc_1.png)

真实光栅化后，用来合成三角形的像素和三角形形状叠加图，用来理论上说明光栅化的原理

![](/img/in-post/2019-07-06-triangle-life/gpu_sc_2.png)

四个像素点为一组，用来像素点的shader处理

![](/img/in-post/2019-07-06-triangle-life/gpu_sc_3.png)

光栅化阶段的优化方式

![](/img/in-post/2019-07-06-triangle-life/gpu_sc_4.png)

#### shader 输入处理器（图元阶段）

![](/img/in-post/2019-07-06-triangle-life/gpu_spi_pp.png)

#### 运算单元（图元阶段）

![](/img/in-post/2019-07-06-triangle-life/gpu_cu_pp.png)

#### shader 输出处理器（图元阶段）

![](/img/in-post/2019-07-06-triangle-life/gpu_se_pp.png)

#### 颜色/深度处理器

![](/img/in-post/2019-07-06-triangle-life/gpu_cdb.png)

#### 整个过程

![](/img/in-post/2019-07-06-triangle-life/gpu_all_detailed.png)

#### 显示到屏幕

![](/img/in-post/2019-07-06-triangle-life/display.png)

## 参考

[triangles are precious](https://gpuopen.com/presentations/2019/nordic-game-2019-triangles-are-precious.pdf)

[AMD Linux Open Source Driver](https://github.com/GPUOpen-Drivers/AMDVLK)

[A trip through the Graphics Pipeline 2011 – Fabian Giesen](https://fgiesen.wordpress.com/2011/07/09/a-trip-through-the-graphics-pipeline-2011-index/)

[Vega Instruction Set Architecture](https://gpuopen.com/amd-vega-instruction-set-architecture-documentation/)

[The AMD GCN Architecture – Layla Mah](https://de.slideshare.net/DevCentralAMD/gs4106-the-amd-gcn-architecture-a-crash-course-by-layla-mah)

[Radeon Southern Islands Acceleration (PM4)](https://developer.amd.com/wordpress/media/2013/10/si_programming_guide_v2.pdf)

[Optimizing the Graphics Pipeline with Compute – Graham Wihlidal](https://frostbite-wp-prd.s3.amazonaws.com/wp-content/uploads/2016/03/29204330/GDC_2016_Compute.pdf)



















