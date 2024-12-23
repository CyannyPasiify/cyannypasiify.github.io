---
title: "[Review] A 3D boundary-guided hybrid network with convolutions and Transformers for lung tumor segmentation in CT images"
collection: "论文评论"
category: "医学图像分割"
permalink: /reviews/2024-10-23-post-a-3d-boundary-guided-hybrid-network-with-convolutions-and-transformers
publish_venue: "Computers in Biology and Medicine (CBM)"
publish_date: 2024-09-01
write_venue: "CBIB, 华中科技大学"
write_date: 2024-10-23
---

精确的肺癌分割能够帮助医生判识肿瘤直径并对肺癌组织进行量化分析。本文提出了一个基于3D-UNet改进的使用3D边界指导的复合网络BGHNet用于肺癌分割，它在自建肺癌数据集、多中心数据集、公开数据集和大规模数据集上都具备良好的分割性能和足够的泛化性能。在BGHNet中，设计了复合式局部-全局上下文特征聚合模块HLGCA，它由并行的卷积分支和Transformer分支构成。卷积分支由金字塔卷积模块（VPConvNeXt）构成，此模块参照Transformer思想改进了卷积结构，因此也能在此分支中兼顾多尺度全局信息；全局分支使用十字交叉窗口Transformer（VSCwin-Transformer），该模块结合了局部偏置和大感受野性质，在降低模型复杂度的同时增强了对局部-全局上下文特征的聚合能力。在解码阶段，设计了边界指导的特征精炼模块（BGFR），该模块能够利用边界信息优化多阶段解码特征，实现了分割区域特征和分割边界特征的有效结合。

# 收录

Computers in Biology and Medicine(CBM) 2024

Liu H, Zhuang Y, Song E, et al. A 3D boundary-guided hybrid network with convolutions and Transformers for lung tumor segmentation in CT images[J]. Computers in Biology and Medicine, 2024, 180: 109009.

[[paper]](https://doi.org/10.1016/j.compbiomed.2024.109009) [[code]](https://github.com/Healingl/BGHNet)

# 贡献点

1. 在编码阶段设计了HLGCA模块，该模块使用并行分支充分学习上下文信息，高效集成了局部卷积算子和全局Transformer算子以捕获聚合局部和全局信息的特征表示，并且提高了感受野覆盖和计算效率。
2. 在解码阶段设计了指导分割网络学习边界信息的BGFR模块和位置感知特征聚合模块PAFF，该模块能够增强肿瘤区域的特征表示。
3. 集成HLGCA和BGFR模块构建BGHNet，该网络使用多尺度损失进行训练，在应对大小规格不一和边界不清晰的肺肿瘤样本时，BGHNet在性能上能够匹及或超过主流方法，并且应用于普通、增强CT图像时都有良好的泛化性能。

# 研究问题

边界指导下的肺癌组织体素级精确分割。肺癌组织内部的分割相对容易，而边界导向准确分割相对困难。这是因为肺癌和周缘组织在边界处存在交叠，在CT图像中的纹理特征和强度分布较为相似，因此肺癌组织的边缘往往难以界定，但边界往往又是临床应用中更为关键的部分。许多模型在边界处存在混淆，不够健壮，从而影响了分割性能。本文使用一个边界检测的辅助任务来指导分割，以提高模型在边界处的分割性能。

# 思想方法

## 研究动机

### 存在领域特定难点

1. 由于肺癌组织具有较大的规格、形状和空间位置的动态范围，且随病人不同在外观上呈现较强的异化性，人工标注肿瘤区域是一个非常耗时、费力和易出错的工作。
2. 肺癌和周缘组织在CT图像中的纹理特征和强度分布均较为相似，因此容易导致复杂和不易区分的肿瘤边缘。

### 主流方法存在缺陷

1. 2D分割方法缺少体积信息，导致无法捕获层间语义信息关联，容易导致高假阳性率。
2. 3D方法虽然能够提取体积维度特征，但依然受制于极端的类别不均衡性和肺癌组织外观的异化性。
3. 现有3D分割方法大多忽略肺肿瘤的边界信息，这导致了不明确的边界预测结果。
4. 某些复合方法集成CNN和Transformer的方法只是简单的并行分支或顺序排列，这使得模型并不能持续性的兼顾局部模式和全局依赖。
5. 有些Transformer技术尝试使用局部窗口划分（SwinT）来兼顾局部信息，这也导致这类模块感受野受限，因此需要大量堆叠Transformer块来增大感受野和实现全局自注意力，从而导致参数量的膨胀和收敛困难。
6. 广泛使用的3D卷积和深度可分离卷积只使用固定大小的核来提取特征，这导致感受野受限并且忽略了多尺度信息。

为了应对以上问题，在编码阶段提出HLGCA模块以协调的方式集成了局部性卷积和全局性Transformer过程，从而能够以更完整的形式提取局部-全局特征。

全局分支VCSwin-Transformer可以在具有局部偏置的条件下学习全局依赖并高效地扩张感受野。VCSwin的平面窗模式在三平面分别执行自注意力计算后聚合，从而压缩计算开销和扩增感受野。

局部分支VPConvNeXt采用改进的可分离卷积模块VPDWC，采用通道划分方式将开销昂贵的大核3D-DConv分解为4个规格不同的DConv，在捕获多尺度全局信息的同时提升计算效率。

解码阶段，实现附加的边界解码器，并应用边界损失构建边界检测辅助任务。通过位置感知特征聚合方法（PAFF）来渐进式地聚合由边界解码器导出的边界特征和由分割解码器导出的区域特征，以优化分割解码器的特征图。

## BGHNet架构

BGHNet架构由包含HLGCA的共享编码器，边界指导模块BGFR，位置感知特征聚合模块PAFF，以及CNN分割解码器构成。前3个编码阶段由3DConv层构成，后接2个HLGCA模块以提取局部和全局上下文特征。在解码器端包含常规的CNN分割解码器以及边界检测辅助任务的BGFR边界解码器，两个解码器的多尺度输出特征通过PAFF聚合。

![BGHNet](/images/reviews/A%203D%20boundary-guided%20hybrid%20network%20with%20convolutions%20and%20Transformers/BGHNet.png)

## HLGCA（Hybrid Local-Global Context Aggregation）模块

HLGCA模块。将输入同时导向卷积和Transformer分支，然后在2路分别进行局部卷积和全局操作，导出特征图后加和。

### 卷积分支（局部分支）

构造了一个ConvNeXt式的深度可分离卷积金字塔（多尺度）模块VPConvNeXt来提取特征。位于首位的VPDWC特征提取模块将一个7x7x7DConv分解为4个并行支：5x5x5小视野卷积，关注局部；7x7x7更大视野卷积，关注更大的局部；9x1x1抽取深度维度特征（抽取横截面间依赖）；1x9x9抽取面特征（抽取横截面内依赖）；最后以通道拼接方式输出。这种分解降低了计算开销，同时实现了多尺度感受野特征聚合，总体感受野大致相当于9x9x9，比原DConv的感受野更大。在后续结构中迁移ConvNeXt的反转瓶颈结构，通过通道放缩来聚合通道特征。

![VPDWC](/images/reviews/A%203D%20boundary-guided%20hybrid%20network%20with%20convolutions%20and%20Transformers/VPDWC.png)

### Transformer分支（全局分支）

该分支中首先通过Vol-To-Seq方法将3D特征图展平为1D序列，嵌入可学习位置编码后通过自注意力模块，最后通过MLP输出。

![HLGCA](/images/reviews/A%203D%20boundary-guided%20hybrid%20network%20with%20convolutions%20and%20Transformers/HLGCA.png)

![VCSwin-SA](/images/reviews/A%203D%20boundary-guided%20hybrid%20network%20with%20convolutions%20and%20Transformers/VCSwin-SA.png)

核心模块为VCSwin-SA模块，与ViT完全全局自注意力和SwinViT局部方形窗口自注意力都不同，VCSwin采用交叉条带窗口提取注意力。每C/3个通道送入一条分支，一共三个分支，分别在三平面条带中执行自注意力操作，最后将结果沿通道拼接。在条带窗口内执行自注意力操作，在一个平面方向上的各条带间是不关联的。这种三平面式的交叉条带自注意力分别只聚合了附近平面的信息，但却以较小的代价将感受野扩展到全局（比3D分块局部方形窗口的Token数量少，但达到了类似全局自注意力的效果）。

## BGFR（Boundary-Guided Feature Refinement）模块

BGFR的目的是通过借助边界检测辅助任务导出的特征图优化常规CNN分割解码器生成的区域特征图，从而实现边界的指导作用。

边界检测辅助任务首先需要从分割蒙版中导出边界标注。通过一次腐蚀操作和一次减去操作提取边缘，腐蚀采用的是各向同N=7规格的结构元，从而提取一个厚度为N/2的边缘作为边界标注。

![PAFF](/images/reviews/A%203D%20boundary-guided%20hybrid%20network%20with%20convolutions%20and%20Transformers/PAFF.png)

![Fusion method comparison](/images/reviews/A%203D%20boundary-guided%20hybrid%20network%20with%20convolutions%20and%20Transformers/Fusion%20method%20comparison.png)

通过PAFF位置感知注意力（即空间注意力）特征聚合模块聚合BGFR导出边界特征和分割解码器生成的区域特征，用这种方法取代简单的加和或通道拼接方式。实验表明PAFF模块能够增强分割区域的激活强度，同时抑制背景激活强度，从而优化用于分割蒙版重建的特征图。

## 实验

使用2套肺癌CT分割数据集来训练和测定性能，包括HUST-Lung自建数据集和公开的MSD-Lung数据集。还使用了多中心数据集和LIDC-IDRI等补充数据集进行额外实验。

### 超参数配置

深度维在第一编码阶段不被下采样，之后各层以2倍下采样，但在深度维每隔1个体素采样1次；其余维度每个阶段都进行2倍下采样。解码阶段上采样使用转置卷积，卷积核规格与编码器端是对称的，上采样方式与下采样对照。

使用SGD优化器训练400周期。学习率0.01，采用多项式学习率衰减，输入为80x160x160的切片。

推理模式下，采用0.5重叠率滑动窗口和测试时增强方案（nnUNet方法的默认方案）。在HUST-Lung数据集上还使用3D组件分析方法，保留最大连通组件。

### 数据集处理

**HUST-Lung数据集**。重采样CT图间距为2mmx1mmx1mm，沿横截面x冠状面x矢状面。裁剪HU值到[-1000, 400]范围，并且线性标准化到[0, 1]。裁剪80x160x160切片用于训练和测试。

**MSD-Lung数据集**。重采样CT图间距为2mmx1mmx1mm，沿横截面x冠状面x矢状面。裁剪HU值到[-1000, 1000]范围，并且线性标准化到[0, 1]。裁剪80x160x160切片用于训练和测试。

### 性能测定

使用HUST-Lung训练集（普通CT）训练的BGHNet在HUST-Lung数据集（普通CT）上取得了84.55%DSC、85.11%SEN、84.87%PPV、3.87mmASSD的性能，在外部多中心数据集（增强CT）上取得了75.73%DSC、74.86%SEN、77.06%PPV、8.43mmASSD的性能，均为对比模型中的最高，验证了BHGNet的高效性。同时，基于普通CT训练的模型在应对增强CT时也能保持足够高的性能，验证了BGHNet的健壮性。

![HUST-Lung Comparison](/images/reviews/A%203D%20boundary-guided%20hybrid%20network%20with%20convolutions%20and%20Transformers/HUST-Lung%20comparison.png)

在公开的小样本肺癌数据集MSD-Lung上取得了78.02%DSC、81.26%SEN、80.40%PPV、5.39mmASSD的性能，除SEN外均为对比模型中最高。在公开的大规模肺结节数据集LIDC-IDRI数据集上，取得了83.14%DSC、89.49%SEN、80.84%PPV、5.03mmASSD的性能，除PPV外均为对比模型中最高。这些结果充分说明了BGHNet在公开度量下的高性能以及多场景下的应用潜力。

![MSD-Lung Comparison](/images/reviews/A%203D%20boundary-guided%20hybrid%20network%20with%20convolutions%20and%20Transformers/MSD-Lung%20comparison.png)

![LIDC-IDRI Comparison](/images/reviews/A%203D%20boundary-guided%20hybrid%20network%20with%20convolutions%20and%20Transformers/LIDC-IDRI%20comparison.png)

# 讨论

## 模型的不足之处

1. 出于控制复杂度的考虑，只在编码阶段最后2层使用了HLGCA模块，而前3层均由常规卷积层构成，这可能限制了全局上下文特征的提取能力。

2. 使用了轻量的PAFF聚合模块，这可能不是最优的，尚不能完全建模复杂的位置依赖关系和边界先验信息。
3. 图像没有采用半监督和自监督学习技术，因此有许多无标签CT图像未得到充分使用，这可能限制了模型的泛化性能。

# 结论

本文提出了一种新的3D分割网络BGHNet，用于从3DCT图像中分割边界模糊、大小可变、生长位置不可预测的肺肿瘤。为了从3DCT图像中学习上下文信息，使用并行卷积和Transformer分支构建HLGCA模块，用于协同建模局部模式和全局依赖关系，其中包含一个使用VPConvNeXt结构的卷积分支和一个使用VCSwin-Transformer结构的Transformer分支。在HLGCA模块的每个分支中，将局部卷积操作与Transformer全局操作结合，在大感受野内高效地聚合局部和全局上下文。

为了应对肿瘤边界模糊导致的性能下降问题，提出BGFR边界指导优化模块，该模块不仅通过辅助的边界检测任务有效指导模型学习边界信息，还能利用提取的边界特征进一步细化特征表示。

大量的实验结果表明，BGHNet方法在普通CT图像中实现了最先进的分割性能，并且显著优于其他基于2D或3D的方法。对于未见的增强CT样本，BGHNet依然能够表现出良好的鲁棒性和泛化性能。

本论文提出的方法有利于临床应用中肺肿瘤的定量分析，可以降低放射科医生对CT扫描进行注释的工作负担。
