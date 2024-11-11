---
title: "[Review] Mamba: Linear-Time Sequence Modeling with Selective State Spaces"
collection: "论文评论"
category: "Mamba"
permalink: /reviews/2024-05-27-mamba-linear-time-sequence-modeling-with-selective-state-spaces
publish_venue: "arXiv preprint"
publish_date: 2024-05-31
write_venue: "CBIB, 华中科技大学"
write_date: 2024-05-27
---

本文提出了一种新的NLP主干网络（Foundation Model，FM），基于结构化状态空间模型（structured State Space Models，SSMs，S4）的改进模型Mamba（S6）。Mamba具有随序列长度线性缩放的推断效率和可并行训练两大优势。Mamba架构在序列感知任务、语言建模、DNA建模、音频建模与生成任务中均展现出匹及Transformer的性能，是极具潜力的序列建模候选主干网络。

# 收录

arXiv preprint

Gu A, Dao T. Mamba: Linear-Time Sequence Modeling with Selective State Spaces[J]. arXiv preprint arXiv:2312.00752, 2023.

[[paper]](https://doi.org/10.48550/arXiv.2312.00752) [[code]](https://github.com/state-spaces/mamba) [[review(own)]](/files/reviews/Mamba%20Linear-Time%20Sequence%20Modeling%20with%20Selective%20State%20Spaces/Mamba动机、思想和技术.pdf)

# 动机

在自然语言、图像、音频等深度学习任务中，Transformer已经成为最主流的主干网络选择，它引入的自注意力（Self-Attention，SA）机制能够对长序列进行高效建模，其性能在诸多下游任务中得到了证实，并且具有良好的缩放（Scale-Up）性能。SA机制实现了有限窗口内的全方位信息流动，从而使Transformer能够对复杂数据关系进行建模，且SA模块内的相关计算可以通过张量乘法方式进行高效的并行实现。然而，SA的实现方式决定了Transformer无法对超出有限窗口的信息进行建模，且计算复杂度为序列长度的平方级别。

RNN是一种经典的时序网络架构，它使用状态编码来记忆前驱序列的信息，通过遗忘门、记忆门等技术逐步更新状态编码，通过输出门生成时序输出。RNN的计算复杂度与序列长度为线性关系，具有很高的推断效率。但是，由于状态更新具有前向依赖性，RNN只能以低效的顺序方式进行训练。RNN和S4模型都是线性时间无关的（Linear Time Invariant，LTI），只设有与输入无关的学习参数，因此这类模型对序列信息的表征能力较差。

**Mamba致力于将Transformer和S4模型的优势汇集于一体，兼具Transformer的高性能和S4模型的高效率，实现一种可并行训练、无窗口限制、表征能力强大、推断开销随序列长度线性缩放的新主干网络。**

# 贡献点

![SSM](/images/reviews/Mamba%20Linear-Time%20Sequence%20Modeling%20with%20Selective%20State%20Spaces/SSM.png)

## 选择性扫描算法

在信息表征方式上，Transformer构建了一种网状的无压缩的全局关联信息矩阵，其规格是信息点元的平方级别。RNN/S4构建了一种LTI的移动加权平均信息矩阵，它的调制参数均为输入无关的静态参数，此矩阵在每个时刻都用统一的方式压缩全局关联信息，对输入无感知，因此表征能力较差。

**Mamba将S4系统中的部分调制参数与输入关联起来，使得关联信息的记录具备输入感知能力，从而能够选择性地记住重要特征，构成局部压缩的关联信息矩阵。这种有感知的调制参数设计在功能上相当于Transformer中的QKV查询聚合机制，使得Mamba具备足够强大且高效的表征学习能力。**

## 硬件感知计算

### 核融合 Kernel Fusion

核融合策略能够降低GPU HBM和高速SRAM之间的IO开销。将SSM模块计算合并到一次往返IO过程中执行，先将SSM参数（Δ, A, B, C）全部预加载到SRAM中，然后连续计算离散化、状态更新和输出结果，最后再一并写回HBM。

### 并行扫描 Parallel Scan

由于SSM参数中的（Δ, B, C）与输入相关，因此无法使用预计算卷积核方式，但仍可以通过并行扫描方式进行加速。由于S6模块是线性系统，其中不包含非线性层，可采用与Blelloch前缀和并行算法相似的思路，将SSM状态更新过程分治为下扫和上扫两个过程，计算层数为Log(L)，L表示序列长度。

![Parallel Scan](/images/reviews/Mamba%20Linear-Time%20Sequence%20Modeling%20with%20Selective%20State%20Spaces/Parallel%20scan.png)

## 简化SSM模块（小改进）

![Mamba Block Evolution](/images/reviews/Mamba%20Linear-Time%20Sequence%20Modeling%20with%20Selective%20State%20Spaces/Mamba%20block%20evolution.png)

将H3和Gated MLP结构杂交为一个更加轻量化的结构，兼具二者的表征能力并提高了模块计算效率。

# 讨论

## 选择性SSM离散建模的代价

SSM本身被定义为一种连续系统的离散化建模方式，因此对连续时序信号（例如音频、视频）的特性具有较强的归纳偏置。选择性SSM（S6）虽然克服了S4在文本、DNA等离散模态任务上的弱点，但在传统SSM所擅长的LTI系统建模任务上，引入选择机制反而不利于提升性能。

## 下游任务生态

相较于拥有成熟下游生态的Transformer，Mamba尚处于起步阶段，在模型微调、适应性调整、提示词化、上下文学习、指令微调、人类反馈强化学习、量化等方面仍有较大发展空间。

## 模型缩放

由于成本限制，在实验环节中采用的Mamba模型规格都较小，这些实验模型的参数量低于绝大多数高性能开源LLM的参数量阈值。大型化Mamba的性能仍是未知数，并且其实现过程可能面临诸多工程挑战。
