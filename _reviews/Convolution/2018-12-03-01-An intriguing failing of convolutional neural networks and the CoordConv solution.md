---
title: "[Review] An intriguing failing of convolutional neural networks and the CoordConv solution"
collection: "论文评论"
category: "卷积算子设计"
permalink: /reviews/2024-11-05-post-an-intriguing-failing-of-convolutional-neural-networks-and-the-coordconv-solution
publish_venue: "Advances in neural information processing systems (NeurIPS), Montreal, Quebec, Canada"
publish_date: 2018-12-03
write_venue: "CBIB, 华中科技大学"
write_date: 2024-11-05
---

**坐标变换任务**要求模型学习一个将笛卡尔空间的坐标(x, y)映射到独热像素空间坐标的映射。本文主要探究了卷积网络在坐标变换任务上的失效问题，通过观察和3项对比实验深度分析了卷积网络的失效原因，并提出CoordConv解决方案。论文设计了有监督坐标分类、坐标回归和渲染3项实验，论证了卷积网络在拟合坐标变换时存在失效和无法泛化的问题。而CoordConv仅仅是在计算时引入额外的坐标通道，即可以更少参数量实现更好的泛化性能。论文还将CoordConv应用于图像分类、目标检测、GAN图像生成和强化学习任务中，论证了CoordConv的普遍适用性以及在具体场景中的应用效果。

# 收录

Advances in neural information processing systems(NeuIPS) 2018

Liu R, Lehman J, Molino P, et al. An intriguing failing of convolutional neural networks and the CoordConv solution[J]. Advances in neural information processing systems, 2018, 31.

[[paper]](https://proceedings.neurips.cc/paper_files/paper/2018/hash/60106888f8977b71e1f15db7bc9a88d1-Abstract.html) [[code]](https://github.com/uber-research/coordconv)

# 贡献点

1. 设计了一个玩具数据集Not-so-Clevr，其中的图像数据是通过在画布上随机铺设正方形构成的。用以验证Conv模型在坐标变换任务上的失效性。
2. 定义了CoordConv层，通过增加额外的坐标信息通道使得卷积核能够感知笛卡尔空间位置。
3. 循序渐进地分析坐标变换问题，从最简单的问题和玩具数据集开始，结束于最复杂的综合任务。
4. 探究更加复杂的有监督渲染任务，要求模型跟据一组正方形中点坐标集合，将整图绘制出来。
5. 在多种任务上进行实验，通过CoordConv替换验证了性能提升。
6. 社区贡献。提供CoordConv模块的代码，以便其它研究者替换使用和研究。

# 研究问题

Conv因为参数集约和具有良好的平移不变性偏置，因而广泛用于各种任务中。但这种平移不变性偏置果真对所有任务都适用吗？有没有可能Conv的特性正在损害各种任务上模型的性能呢？

本文将针对从致密的笛卡尔空间表示到稀疏像素表示（或相反）的空间变换问题，阐释和分析CNN在表达空间变换上的通用失效性。

1. 深度探究几何信息注入的必要性。
2. 将研究视野最小化到坐标变换任务上来，归纳出一个简洁而不失一般性的解释，将早期工作零散的观测结果统一起来。
3. 提出一个可以多种形式应用于其它任务的统一模块，使其可被轻松集成到卷积网络的各处。

# 思想方法

## 研究动机

实验表明仅仅是简单嵌入坐标信息，CoordConv既能够通过学习保持平移不变性，也可以使网络具有可变程度的平移依赖性。在坐标变换任务上，CoordConv能够以卷积网络10-100分之一的参数，实现150倍的速率，同时取得近乎完美的泛化性能。

CoordConv与标准Conv的鲜明对比引发了一个疑问：卷积在坐标映射上的失效性是否在其它任务中依然存在，并且损害了模型性能？受此问题影响的各种任务的覆盖面究竟有多大？

事实上，在很多改良模型的设计中，研究员可能是通过观察或直觉发现了标准CNN在特定任务上的一些失效点或误导性的归纳偏差，然后设计了一种解决方案。但这些思路或直觉并没有在以往论文工作中呈现出来，或者只是作为次要内容简单提及，甚至于研究员自己也不清楚。

本论文试图通过详尽的分析观察和实验来系统性的阐明CNN在坐标变换任务上的失效性。

## Not-so-Clevr数据集

Not-so-Clevr数据集是Sort-of-CLEVR数据集的一个简化版，样本均为只包含单目标、灰度的3D形状渲染结果。数据集中的样本为64x64的图像，在上面绘制了一个9x9区域的白色正方形。

Not-so-Clevr数据集包含3个属性对象：

- 中心点坐标。
- 中心坐标独热（白色点）的图像。
- 在中心点坐标绘制了正方形的渲染图像。

这3个属性对象为论文中的3项实验，即有监督坐标分类、有监督坐标回归和有监督渲染提供支持。

## CoordConv层构成和性质分析

在通常任务中，卷积核能够良好地工作，这可能包括三方面原因：
1. 相对较少的可学习参数。
2. 在现代GPU上能够高效计算。
3. 目标函数具备平移不变性。

CoordConv在前两个特性（较少的参数量和高计算效率）上具备与Conv相同的优势，还可以根据需求注入或舍弃平移不变性先验。虽然通常认为引入坐标信息会导致Conv失去平移不变性先验，有可能损害模型的泛化性能。
但本文后续的论据说明这种针对非平移不变性的建模反而能更好地训练模型和提升泛化性能。

![CoordConv layer](/images/reviews/An%20intriguing%20failing%20of%20convolutional%20neural%20networks%20and%20the%20CoordConv%20solution/CoordConv%20layer.png)

**注入几何信息是可扩展的**。在二维图像卷积上，一个二维(x, y)坐标即可完全表征一个像素的位置，可以向数据图拼接2个坐标通道来注入坐标信息。还可以增加更多的通道来引导模型学习特定任务的解决方案。例如，添加中心距坐标来增加对图像中心的距离感知。

**参数量**。CoordConv相较于Conv仅仅增加了d个通道的参数，增幅很小。

**平移不变性**。当额外注入的通道维权重初始化或学习为0值时，CoordConv是平移不变的。如果通道维权重不为0，CoordConv将包含一些平移依赖信息，其具体形式与目标任务相关（例如某些任务可能在特定的坐标区域内存在ROI）。CoordConv针对坐标维的参数也是所有位置共享的，因此其平移依赖性只来自于坐标信息的定义。而不像局部连接层（每个局部都有一套单独参数）那样，在每个局部区块中使用不同的参数。

**相关工作**。CoordConv的基本思想来源于CPPN，CPPN根据输入的坐标信息维度数据，输出N维数据，即绘制N个分离的灰度图像。CoordConv则继续扩展了这一点，它使用的原始数据信息不只是坐标信息，还包括像素数据信息。某些使用卷积的序列学习任务也在输入层使用位置嵌入技术（比如Transformer），但它们的嵌入形式是对过完备基按位置信息加权后加和，CoordConv则是直接拼接坐标信息为额外通道。

> **过完备基**：一组向量，其的数量大于数据维度。这组向量不是基向量组，因为其中必有某个向量可用其它向量的线性组合表示。

许多早期工作发现将几何信息注入到网络中是引导模型完成任务的必要手段。例如，以增强空域平滑度、分割为目的的先验网络，通过空域softmax层以及将场景映射到对象位置的坐标层实现的机器人控制技术。然而绝大多数论文只是将几何信息注入视作一种优化特定任务的末梢技术，并未给予过多关注或讨论。本文则将此问题作为核心研究内容，尽可能深入地探究注入几何信息的必要性。

## 实验

对Not-so-Clevr数据集采用2种方式进行划分：

1. **均匀采样划分数据集**。测试集中的样本都可以在训练集中找到非常接近的样本。（易泛化）
2. **四象限划分**。以画布中心点为原点，训练集样本中心坐标全部位于第1、2、3象限，测试集样本全部位于第4象限。（难泛化）

### 有监督坐标分类

**任务描述**。给定一个输入坐标(x, y)，模型学习如何在画布上准确绘制对应的坐标像素。此任务可视为一个多分类任务，每个像素点都是一个单独的类。

**实验结果**。即便是在均匀采样数据集上，Conv网络（200k参数，训练超过1h）也仅能达到86%准确率，无法进一步提升。这一结果并不理想，因为均匀采样前提下，几乎所有测试样本都与训练样本非常接近。而在四象限划分时，Conv模型完全无法泛化。这些结果表明，CNN拟合一个从(x, y)坐标映射到独热像素的光滑函数是相当困难的，即便是在提供完全覆盖各种情形样本的有监督情况下。<u>相对的</u>，CoordConv模型在任一种划分情况下都快速收敛达到了几乎完美（~100%准确）的性能，且只需要更少的参数量（7.5k）和更少的训练时间（10-20s）。

### 有监督坐标回归

**任务描述**。给定只有一个像素高亮的输入图像，要求模型输出高亮像素的坐标(x, y)。

**实验结果**。4层Conv+全连接层网络在均匀划分时泛化表现良好，但在四象限划分时完全失效。一个更小的全卷积模型可以在四象限划分时实现有限的泛化性，但却在均匀划分时表现很差。<u>相对的</u>，一个900参数的CoordConv模型（一个CoordConv层后接几个标准Conv层）可在两种划分情况下均快速收敛和良好泛化。

![Supervised coordinate classification and regression](/images/reviews/An%20intriguing%20failing%20of%20convolutional%20neural%20networks%20and%20the%20CoordConv%20solution/Supervised%20coordinate%20classification%20and%20regression.png)

### 有监督渲染

要求网络在64x64画布上根据给定的(x, y)坐标位置绘制正方形。结果与之前的2个任务是类似的，说明Conv确实难以胜任坐标变换任务。

# 讨论

## 在各类任务中的应用

**图像分类任务**。这类任务存在明显的平移不变性需求，CoordConv对此类任务性能几乎无影响，也不会造成明显损害。

**目标检测任务**。这类任务和坐标变换有着自然联系，实验表明CoordConv的引入确实有利于此任务，在Faster R-CNN的替换实验中实现了24%的IOU性能提升。

**图像合成任务**。在针对简单的2个带颜色几何形状渲染图像的合成任务中，ConvGAN出现了从二维分布到一维流形的模式崩溃。模式分布（两个几何形状中心点位置散点）崩溃到一个圈环上去了。而CoordConv GAN则能够生成对2D空间覆盖更全面的图像集合，并且仅仅使用了ConvGAN7%的参数。

![Real images and generated images by GAN and CoordConv GAN](/images/reviews/An%20intriguing%20failing%20of%20convolutional%20neural%20networks%20and%20the%20CoordConv%20solution/Real%20images%20and%20generated%20images%20by%20GAN%20and%20CoordConv%20GAN.png)

**强化学习**。将CoordConv用于强化学习时，在Atari游戏的6个项目中表现更好，2个持平和1个略微劣势。

# 结论

本文展现了CNN模型在坐标变换任务中的失效性，并且提出了一个简洁的修复方案，即使用嵌入坐标信息的CoordConv层。CoordConv是应对坐标变换任务的解决方案，并且易于嵌入到各类模型中去，实验结果表明在许多实际任务的模型架构中加入CoordConv层都能够取得一定的性能提升。
