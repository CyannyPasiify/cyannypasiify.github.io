---
title: "[Review] 3DConvCaps: 3DUnet with convolutional capsule encoder for medical image segmentation"
collection: "论文评论"
category: "医学图像分割"
permalink: /reviews/2024-04-11-post-3dconvcaps-3dunet-with-convolutional-capsule-encoder-for-medical-image-segmentation
publish_venue: "International Conference on Pattern Recognition (ICPR), Montreal, Quebec, Canada"
publish_date: 2022-11-29
write_venue: "CBIB, 华中科技大学"
write_date: 2024-04-11
---

本文提出了一种基于3D体素的胶囊网络3DConvCaps，应用于3D医学图像分割任务中。在编码器浅层使用CNN提取局部特征，在深层使用胶囊网络提取高级特征并对局部-整体特征进行建模。

# 收录

ICPR 2022

Tran M, Vo-Ho V K, Le N T H. 3DConvCaps: 3DUnet with convolutional capsule encoder for medical image segmentation[C]//2022 26th International Conference on Pattern Recognition (ICPR). IEEE, 2022: 4392-4398.

[[paper]](https://doi.org/10.1109/ICPR56361.2022.9956588) [[code]](https://github.com/UARK-AICV/3DConvCaps)

# 要点

- 提出3DConvCaps：在Encoder上对3D-UCaps进行改进，减少参数和加速推断。

# 研究理由

虽然胶囊网络能够很好地提取局部-整体特征，但其复杂的路由算法导致推断效率偏低。在网络的浅层，CNN能够胜任局部特征提取工作，因此使用胶囊网络对局部-整体特征进行建模可以下移至深层。

# 主要内容

## 架构

Encoder浅层使用CNN提取局部特征，在深层使用胶囊网络提取高级特征并对局部-整体特征进行建模。Decoder使用CNN。

## 训练

将GT下采样后和胶囊编码器输出一起用于计算边界损失（Margin Loss）。

在编码器后使用加权交叉熵损失，并使用一条附加路径重建原始图像，计算Masked MSE Loss。

# 结论

提出一种高效的网络架构3DConvCaps，该架构继承了胶囊卷积和CNN的优点，前者在高级特征上对部分-整体关系进行建模，后者在低级特征下学习短距离信息。与其它主流胶囊网络和基于3D CNN的MIS方法相比，3DConvCaps在性能和网络效率上都更高，具有更好的鲁棒性。