Comparison of Video Quality Assessment Methods

通常视频质量评价可以分为两类：客观质量评价和主观质量评价。

客观质量评价可以分为三类：

- Full reference（FR）
- Reduces reference（RF）
- No reference（NR）

其中，无参的方法可以进一步分为三类：

- pixel based（基于像素）：具有更高的计算量
- Bitstream Based（基于比特流）：计算量更低，但是只适用于特定的编码技术和比特流格式。
- Hybrid of pixel and bitstream（同时基于像素和比特流）

基于比特流的无参质量评价又可以分为三类：

- parametric planning model：不能获得码流，但是可以利用码率，codec类型和丢包率信息，具有更低的复杂度。[示例](https://ieeexplore.ieee.org/document/7781647/citations#citations)
- parametric packet-layer model：可以获得完整的码流，并且能够从码流中解析出码率，帧级别，帧率，帧类型和丢包率等。其作为基于QoS的一种方法为人所熟知。[示例]( https://ieeexplore.ieee.org/document/5605528 )
- bitstream layer model：除了像素信息外几乎可以使用所有的参数。如码率，帧率，QP，DCT系数，运动向量等，具有更好的性能但是更加复杂。[示例]( http://bth.diva-portal.org/smash/record.jsf?pid=diva2%3A833999&dswid=-8464 )

#### Video Quality Metrics（VQM）

#### MSE—最小均方误差(Mean Squared Error)

<div align=center>  <img src="pics\Comparison-of-VQA-Methods\1.PNG" alt="image" style="zoom: 100%;" /></div>

MSE的取值范围是0-65025，值越小，说明质量越好

#### PSNR—Peak Signal to Noise Ratio

<div align=center>  <img src="pics\Comparison-of-VQA-Methods\2.PNG" alt="image" style="zoom: 100%;" /></div>

PSNR取值范围是0(max difference) — ∞(no difference)。但是在实际场景中PSNR值一般不超过50dB。PSNR值越大，说明质量越好。

#### SSIM—结构相似性（Structural Similarity Index Measure）

表征两张图像之间的相似性。包含了三种失真：光照，对比度和纹理。范围是-1—1，值越大说明质量越好。

#### VSSIM—视频结构相似性

<div align=center>  <img src="pics\Comparison-of-VQA-Methods\3.PNG" alt="image" style="zoom: 100%;" /></div>

为每一帧图像的SSIM，Rs为每一帧图像的采样窗口数。


<div align=center>  <img src="pics\Comparison-of-VQA-Methods\4.PNG" alt="image" style="zoom: 100%;" /></div>

#### PVQM—Perceptual Video Quality Metric

估计感知质量，是失真指标的线性组合，如边缘，时间去相关和颜色误差。

#### VQM—Video Quality Model

RF方法的一种。

#### PEVQ—Precetual Evaluation of Video Quality