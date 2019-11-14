## Inverse Tone Mapping

### Introduction

由于HDR图像的大量出现，而传统设备只能支持SDR，所以如何将HDR转为SDR是一个非常重要的工作，即TMO（Tone Mapping Operator）。这些方法可以分为两类：

- 全局：对全局使用相同的映射函数、
- 局部：对每个像素点计算邻域的光照强度，然后根据其进行映射。最难的部分是如何正确确定邻域的大小，否则可能会出现振铃效应和光晕现象。

还有一类TMO试图模仿HVS来选择映射函数。

与Tone Mapping相对应的就是Inverse Tone Mapping（逆色调映射）。本文介绍了一种逆映射的方法。ITMO可以增强非HDR媒体素材从而使得其可以在HDR设备上播放。此外，ITMO在一些领域也有着一定的应用。

### Related Work

在各大图形网站上有许多技术和教程都对图像进行了扩展，但是这些技术可以被归类为像素级的线性或指数扩展。这种方法的主要问题是不知道相邻像素的空间信息，这意味着不知道光源的位置，这可能会导致噪音问题，以及在低亮度区域周围会产生一些明亮像素，如下图所示。

  <div align=center>  <img src="pics\Inverse-Tone-Mapping\1.PNG" alt="image" style="zoom: 70%;" /></div>
[Bennett and McMillan 2005]提出了一种视频处理技术，其中HDR概念被用来提高视频质量以去除噪声和曝光不足的视频进行色调映射。但是他们并没有研究从LDR产生HDR视频。

本文的工作可以与现有的图像编辑无约束技术相关联，用户可以任意改变图像中选定对象的材料属性，或者是用于灰度图像的着色算法。

### Outline

算法流程如下图所示，共有四步：

- 使用Inverse Tone Mapping 和线性插值的方式产生一个初始的HDR图像

- 应用中位切分算法（Median Cut Algorithm）查找图像中的高光照区域
- 使用上一步中位切分算法对高亮度区域进行密度估计生成增强图
- 第一步产生的初始HDR图像结合第三步的增强图作为插值权重，产生最终的HDR图

  <div align=center>  <img src="pics\Inverse-Tone-Mapping\2.PNG" alt="image" style="zoom: 70%;" /></div>



#### Inverse Tone Mapping Operator

最近发表的TMO通常使用世界亮度Lw并且生成一系列光照值Ld，世界亮度的定义为：
$$
L_w=0.213R_w+0.715G_w+0.072B_w
$$


压缩后的像素值表达式为：
$$
\left[ \begin{matrix} R_d \\ G_d\\ B_d \end{matrix} \right]=\left[ \begin{matrix} L_d\frac{R_w}{L_w} \\ L_d\frac{G_w}{L_w}\\ L_d\frac{B_w}{L_w} \end{matrix} \right]
$$
计算结果分别对应压缩后的RGB像素值。同理，将上述公式整理，便可以得到ITMO的表达式。
$$
\left[ \begin{matrix} R_w \\ G_w\\ B_w \end{matrix} \right]=\left[ \begin{matrix} L_w\frac{R_d}{L_d} \\ L_w\frac{G_d}{L_d}\\ L_w\frac{B_d}{L_d} \end{matrix} \right]
$$
但是，问题在于，由于从LDR图像入手，没有Lw的信息。

LDR图像的扩展问题有很多，比如像素的缩放或指数函数，但是这些通常不会产生令人信服的结果。所以本文提出了一种更加精确的方式来进行逆映射。

本文的第一步是选择一个TMO：目前有一些并且每一种都有各自的优缺点。ITMO选择的是Photographic Tone Reproduction。主要的原因是他非常容易反转，并且是流行的TMO算法，在压缩的对比度损失比其他的TMO表现得更好。下面介绍一下Photographic Tone Reproduction。

首先它使用几何平均对图像的像素进行缩放，这是对场景的近似，用于表明场景是亮、正常还是暗的。缩放公式为：
$$
L_m(x,y) = \frac{\alpha}{\overline L_w}L_w(x,y)
$$
其中α是从图像中估计出来的参数，其中的集合平均定义为：
$$
\overline L_w = exp(\frac{1}{N}\sum_{x,y}log(\delta+L_w(x,y)))
$$
最终缩放后的像素值的表达式为：
$$
L_d(x,y)=\frac{L_m(x,y)}{1+L_m(x,y)}
$$
但是，这个函数的主要问题是最大亮度的像素值没有办法映射到1，这可以通过将上式进行线性映射得到解决：
$$
L_d(x,y)=\frac{L_m(x,y)(1+\frac{L_m(x,y)}{L_{white}^2})}{1+L_m(x,y)}
$$
为了进行逆映射，需要根据Ld的表达式反解出：
$$
L_m(x,y)=\frac{L_d(x,y)}{1-L_d(x,y)}
$$
将缩放公式代入得：
$$
L_w(x,y)=\frac{L_d(x,y)\overline L_w}{(1-L_d(x,y))\alpha}
$$
上式的一个问题是将亮度为1的像素被映射到无穷，因袭需要增加一个阻尼系数。第二个问题是

不太好控制好扩大的范围，非常高的亮度值被映射到最大值上，中低亮度像素值被映射到非常低的亮度值。如下图所示。其中(a)是原始图像，(b)是最大亮度值映射到20，(c)是最大亮度值映射到200。

<div align=center>  <img src="pics\Inverse-Tone-Mapping\3.PNG" alt="image" style="zoom: 50%;" /></div>

因此本文并没有选择这种方法，而是选择求解Lm的方程。新公式是Lm的二阶方程，很容易求解，但是由于是从LDR图像入手，所以有很多的未知量，因此需要对一些未知量进行估计。然后文中讲述了对一些量的具体的估计方法和公式表达，最终对二次方程进行了求解。

这种ITMO的主要优点是解函数是平滑的，所以他可以避免很多噪声，但一个缺点是不能任意扩大范围，只能扩展到中等动态范围。否则，如果最高亮度设置得比较高，那么块效应会非常明显。解决这问题的一个方法是创建一个插值表，对高亮度区域进行插值。这个表就称为Expand Map，由高亮度区域根据中位切分算法得到的密度估计结果产生。

#### Finding Light Sources

中位切分算法可以用来对光源进行采样，该算法的优点是将光源聚集在高亮度区域附近。如下图所示：

<div align=center>  <img src="pics\Inverse-Tone-Mapping\4.PNG" alt="image" style="zoom: 70%;" /></div>