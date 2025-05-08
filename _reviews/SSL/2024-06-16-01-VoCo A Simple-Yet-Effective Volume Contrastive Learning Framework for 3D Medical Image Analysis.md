---
title: "[Review] VoCo: a simple-yet-effective volume contrastive learning framework for 3D medical image analysis"
collection: "论文评论"
category: "自监督学习"
permalink: /reviews/2025-05-08-post-voco-a-simple-yet-effective-volume-contrastive-learning-framework-for-3d-medical-image-analysis
publish_venue: "Conference on Computer Vision and Pattern Recognition (CVPR), Seattle, Washington, USA"
publish_date: 2024-06-16
write_venue: "理想家园, 南昌"
write_date: 2025-05-08
---

VoCo即本文提出的**Vo**lume **Co**ntrast Framework自监督学习（SSL）框架，是一种以3D医学图像为处理目标的3D网络预训练方法。其特点是能够让模型对上下文位置信息（一种相对位置语义）进行有效学习，从而在3D医学图像分割、分类等对高级语义信息具有较高需求的下游任务中提升模型性能。

# 收录

2024 IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR)

Wu L, Zhuang J, Chen H. VoCo: a simple-yet-effective volume contrastive learning framework for 3D medical image analysis[C]//Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition. 2024: 22873-22882.

[[paper]](https://doi.org/10.1109/CVPR52733.2024.02158) [[paper(self copy)]](/files/reviews/VoCo_MedSSL/VoCo%20a%20simple-yet-effective%20volume%20contrastive%20learning%20framework%20for%203d%20medical%20image%20analysis.pdf) [[code]](https://github.com/Luffy03/VoCo)

# 贡献点

1. 设计了VoCo框架，使用每个样本进行学习，将其划分为若干基切片，每个基切片代表一个分类，再随机切片并计算其与基切片的相似度，使用随即切片与基切片的重叠体积作为监督标签。此方法能够让模型习得跨切片的上下文位置语义信息。
2. 设计了同时优化切片分类准确度（预测损失）和降低基类别相关性（正则损失）的损失函数。首先将每个切片嵌入为Token，然后计算随机切片与所有基切片的余弦相似度（CosSim），使用log-L1损失进行优化。采用正交基思想，两两组合计算每对基切片Token间的CosSim，以总相似度最小化为优化目标尽量消除基切片间的相关性。
3. 基于BTCV、TCIA Covid19、LUNA三个CT数据集上进行SSL，然后在LiTs、MSD Spleen、MM-WHS三个CT数据集上优化测定性能，在非CT模态的BraTS 21数据集上验证VoCo的通用性，最后使用CC-CCII数据集验证VoCo在分类任务中的有效性。比较对象包括直接训练模型，采用MAE、MoCo通用SSL的模型，以及采用其它医学图像SSL方法的模型。VoCo模型均取得了显著性能优势，甚至将其应用于经典3DUNet模型时也能取得次优结果（依旧超越不使用VoCo-SSL方法的模型）。

# 研究问题

医学图像有一个重要的先验知识，就是各类器官的相对位置和形态是比较稳定的。但是大多数基座模型并未仔细考虑这一先验。传统的SSL方法，例如“根据切片预测坐标”、“增强图像/掩蔽一部分后重建”，这些体素重建的SSL监督使用了大量体素级（低级）信息，对高级语义的学习存在不足。作者认为3D医学图像的上下文位置信息这种反映相对位置关系的高级语义应当被更充分地利用，因此设计了VoCo方法。

值得一提的是，VoCo可以帮助下游任务模型实现一定程度的跨越切片的语义信息交换，它是通过模型参数记忆实现的。虽然不是切片间信息的直接交换（下游任务训练时可视范围仅限于目标切片），但确实间接地让模型具备了推断周边上下文（目标切片周围的模型不可视区域）的能力。

# 方法

## 架构

如图所示VoCo包含两类切片和两条监督路径。

两类切片。首先将全图均匀划分为一系列互不重叠的基切片（base crops），如图中的4x4x1网格。然后随机切片（randomly crop），据此生成自监督标签用于优化。所有切片都会通过主干（Backbone）编码器嵌入为特征向量，图中$p$表示随机切片的嵌入向量，$q_i$表示每个基切片的嵌入向量，每个基切片的嵌入向量代表一个伪类。

两条监督路径。右上方的预测路径（Prediction）中计算随机切片与每个基切片的相似度，并向自监督标签优化，其logits结果反映了随机切片属于哪些伪类。右下方的正则化路径（Regularization）两两成对计算基切片间的相似度，并尽量拉开基切片的语义间距，将它们向彼此无关（正交）的方向优化，这一路径的目的是让每个伪类变得能够区分，成为事实上可分的类。

![VoCo_Arch](/images/reviews/VoCo%20a%20simple-yet-effective%20volume%20contrastive%20learning%20framework%20for%203D%20medical%20image%20analysis/VoCo_arch.png)

## 自监督标签生成

随机切片的规格与基切片相同，它最多可与4个基切片部分重叠，如图所示。根据随机切片与基切片的重叠体积赋予标签值，例如图中随机切片在5、6、9、10类上的标签值依次是0.25、0.1、0.5、0.15，而在其它类上的标签值都是0。

![label_gen](/images/reviews/VoCo%20a%20simple-yet-effective%20volume%20contrastive%20learning%20framework%20for%203D%20medical%20image%20analysis/label_gen.png)

## 相似度计算和损失函数

使用余弦相似度计算随机切片嵌入向量$p$，每个基切片的嵌入向量$q_i$的相似度$l_i$，即

$$
l_i={CosSim}\left(p,q_i\right)=\frac{p\cdot q_i}{\lVert p \rVert \lVert q_i \rVert},i \isin n
$$

给定自监督n类标签向量$y$（$y_i$表示第i类的标签值），预测路径的损失函数为

$$
L_{pred}=-\frac 1 n \sum^{n}_{i\isin n}{log}\left(1-\lvert y_i-l_i \rvert\right)
$$

类似的，使用余弦相似度计算每对基切片间的相似度，基切片i,j间的相似度$s_{ij}$为

$$
s_{ij}={CosSim}\left(q_i,q_j\right)=\frac{q_i\cdot q_j}{\lVert q_i \rVert \lVert q_j \rVert},{i,j} \isin n, i\ne j
$$

正则路径的损失函数体现为这些成对基切片相似度的总和

$$
L_{reg}=-\frac 2 {n\left(n-1\right)} \sum^{n}_{ {i,j} \isin n, i\ne j }\lvert s_{ij} \rvert
$$

正则路径优化的最终目标是让所有基切片的嵌入向量（伪类表征）两两正交，即

$$
q_i \perp q_j,{i,j} \isin n, i\ne j
$$

最终损失函数为（$\lambda$默认取1）

$$
L=L_{pred}+\lambda L_{reg}
$$

## 预训练和迁移用途

在预训练数据集上通过前述方式进行VoCo框架的SSL训练，然后将预训练权重迁移到具体的下游任务模型中，使用下游任务数据集继续优化训练。论文主要使用Swin-UNETR、3DUNet作为VoCo的主干网络进行了实验。

# 实验和结论

论文使用BTCV、TCIA Covid19、LUNA进行VoCo和其它对比方法的自监督训练，然后在LiTs、MSD Spleen、MM-WHS三个CT数据集上进行下游任务训练和测定性能，与直接训练模型UNETR、Swin-UNETR和SSL预训练+迁移学习模型MAE3D、SimCLR、SimMIM、MoCo v3、Jigsaw、PositionLabel、MG、ROT、Vicregl、Rubik++、PCRL、Swin-UNETR（SSL模式）、SwinMM、GL-MAE、GVSL进行对比，VoCo皆取得了最优性能。Swin-UNETR主干的VoCo模型在BTCV上取得了83.85%DICE；在LiTs上取得96.52%DICE，排第二的3DUNet主干VoCo模型取得96.03%DICE；在MSD Spleen上取得96.34%DICE；在MM-WHS上取得90.54%DICE。

此外，VoCo模型虽然是在CT模态下预训练的，在相同设定下，它在MRI模态数据集BraTS 21中也取得了对比模型中最优的78.53%DICE性能。在CC-CCII分类数据集上依然有最优的90.83%ACC准确率。

由此可见，上下文位置信息对于3D医学图像任务而言非常重要，尤其是在面临3D体素这种大规格数据，必须将数据切片后再用于训练的情况下。让模型基于单个切片视野就能预测周围切片语义（或者说更准确地认知自身所包含的相对位置语义）对分割和分类任务皆有指导价值。

# 讨论

## VoCo系列SSL为切片间信息交换提供了一种隐式途径

VoCo可以帮助下游任务模型实现一定程度的跨越切片的语义信息交换，它是通过模型参数记忆实现的。虽然不是切片间信息的直接交换（下游任务训练时可视范围仅限于目标切片），但确实间接地让模型具备了推断周边上下文（目标切片周围的模型不可视区域）的能力。
