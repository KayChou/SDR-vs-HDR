## Inverse Tone Mapping

### Introduction

由于HDR图像的大量出现，而传统设备只能支持SDR，所以如何将HDR转为SDR是一个非常重要的工作，即TMO（Tone Mapping Operator）。这些方法可以分为两类：

- 全局：对全局使用相同的映射函数、
- 局部：对每个像素点计算邻域的光照强度，然后根据其进行映射。最难的部分是如何正确确定邻域的大小，否则可能会出现振铃效应和光晕现象。

还有一类TMO试图模仿HVS来选择映射函数。

与Tone Mapping相对应的就是Inverse Tone Mapping（逆色调映射）。本文介绍了一种逆映射的方法。ITMO可以增强非HDR媒体素材从而使得其可以在HDR设备上播放。此外，ITMO在一些领域也有着一定的应用。

### Related Work

在各大图形网站上有许多技术和教程都对图像进行了扩展，但是这些技术可以被归类为像素级的线性或指数扩展。这种方法的主要问题是不知道相邻像素的空间信息，这意味着不知道光源的位置，这可能会导致噪音问题，以及在低亮度区域周围会产生一些明亮像素，如下图所示。

<img src="pics\Inverse-Tone-Mapping\1.PNG" alt="image" style="zoom: 67%;" />

[Bennett and McMillan 2005]提出了一种视频处理技术，其中HDR概念被用来提高视频质量以去除噪声和曝光不足的视频进行色调映射。但是他们并没有研究从LDR产生HDR视频。

本文的工作可以与现有的图像编辑无约束技术相关联，用户可以任意改变图像中选定对象的材料属性，或者是用于灰度图像的着色算法。

### Outline

算法流程如下图所示，共有四步：

- 使用Inverse Tone Mapping 和线性插值的方式产生一个初始的HDR图像

- 应用中位切分算法（Median Cut Algorithm）查找图像中的高光照区域
- 使用上一步中位切分算法对高亮度区域进行密度估计生成增强图
- 第一步产生的初始HDR图像结合第三步的增强图作为插值权重，产生最终的HDR图

<img src="pics\Inverse-Tone-Mapping\2.PNG" alt="image" style="zoom: 67%;" />



