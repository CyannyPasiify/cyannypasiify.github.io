---
title: "A 3D hierarchical cross-modality interaction network using transformers and convolutions for brain glioma segmentation in MR images"
collection: publications
category: "期刊论文"
permalink: /publication/2024-08-13-01-a-3d-hierarchical-cross-modality-interaction-network-using-transformers-and-convolutions-for-brain-glioma-segmentation-in-mr-images
excerpt: '# Abstract

## Background

Precise glioma segmentation from multi-parametric magnetic resonance (MR) images is essential for brain glioma
diagnosis. However, due to the indistinct boundaries between tumor sub-regions and the heterogeneous appearances of
gliomas in volumetric MR scans, designing a reliable and automated glioma segmentation method is still challenging.
Although existing 3D Transformer-based or convolution-based segmentation networks have obtained promising results via
multi-modal feature fusion strategies or contextual learning methods, they widely lack the capability of hierarchical
interactions between different modalities and cannot effectively learn comprehensive feature representations related to
all glioma sub-regions.

## Purpose

To overcome these problems, in this paper, we propose a 3D hierarchical cross-modality interaction network (HCMINet)
using Transformers and convolutions for accurate multi-modal glioma segmentation, which leverages an effective
hierarchical cross-modality interaction strategy to sufficiently learn modality-specific and modality-shared knowledge
correlated to glioma sub-region segmentation from multi-parametric MR images.

## Methods

In the HCMINet, we first design a hierarchical cross-modality interaction Transformer (HCMITrans) encoder to
hierarchically encode and fuse heterogeneous multi-modal features by Transformer-based intra-modal embeddings and
inter-modal interactions in multiple encoding stages, which effectively captures complex cross-modality correlations
while modeling global contexts. Then, we collaborate an HCMITrans encoder with a modality-shared convolutional encoder
to construct the dual-encoder architecture in the encoding stage, which can learn the abundant contextual information
from global and local perspectives. Finally, in the decoding stage, we present a progressive hybrid context fusion (
PHCF) decoder to progressively fuse local and global features extracted by the dual-encoder architecture, which utilizes
the local-global context fusion (LGCF) module to efficiently alleviate the contextual discrepancy among the decoding
features.

## Results

Extensive experiments are conducted on two public and competitive glioma benchmark datasets, including the BraTS2020
dataset with 494 patients and the BraTS2021 dataset with 1251 patients. Results show that our proposed method
outperforms existing Transformer-based and CNN-based methods using other multi-modal fusion strategies in our
experiments. Specifically, the proposed HCMINet achieves state-of-the-art mean DSC values of 85.33% and 91.09% on the
BraTS2020 online validation dataset and the BraTS2021 local testing dataset, respectively.

## Conclusions

Our proposed method can accurately and automatically segment glioma regions from multi-parametric MR images, which is
beneficial for the quantitative analysis of brain gliomas and helpful for reducing the annotation burden of
neuroradiologists.'
date: 2009-10-01
venue: 'Medical Physics'
paperurl: 'https://doi.org/10.1002/mp.17354'
codeurl: 'https://github.com/Healingl/HCMINet'
citation: 'Zhuang Y, Liu H, Fang W, et al. A 3D hierarchical cross‐modality interaction network using transformers and convolutions for brain glioma segmentation in MR images[J]. Medical Physics, 2024, 51(11): 8371-8389.'
---

# Abstract

## Background

Precise glioma segmentation from multi-parametric magnetic resonance (MR) images is essential for brain glioma
diagnosis. However, due to the indistinct boundaries between tumor sub-regions and the heterogeneous appearances of
gliomas in volumetric MR scans, designing a reliable and automated glioma segmentation method is still challenging.
Although existing 3D Transformer-based or convolution-based segmentation networks have obtained promising results via
multi-modal feature fusion strategies or contextual learning methods, they widely lack the capability of hierarchical
interactions between different modalities and cannot effectively learn comprehensive feature representations related to
all glioma sub-regions.

## Purpose

To overcome these problems, in this paper, we propose a 3D hierarchical cross-modality interaction network (HCMINet)
using Transformers and convolutions for accurate multi-modal glioma segmentation, which leverages an effective
hierarchical cross-modality interaction strategy to sufficiently learn modality-specific and modality-shared knowledge
correlated to glioma sub-region segmentation from multi-parametric MR images.

## Methods

In the HCMINet, we first design a hierarchical cross-modality interaction Transformer (HCMITrans) encoder to
hierarchically encode and fuse heterogeneous multi-modal features by Transformer-based intra-modal embeddings and
inter-modal interactions in multiple encoding stages, which effectively captures complex cross-modality correlations
while modeling global contexts. Then, we collaborate an HCMITrans encoder with a modality-shared convolutional encoder
to construct the dual-encoder architecture in the encoding stage, which can learn the abundant contextual information
from global and local perspectives. Finally, in the decoding stage, we present a progressive hybrid context fusion (
PHCF) decoder to progressively fuse local and global features extracted by the dual-encoder architecture, which utilizes
the local-global context fusion (LGCF) module to efficiently alleviate the contextual discrepancy among the decoding
features.

## Results

Extensive experiments are conducted on two public and competitive glioma benchmark datasets, including the BraTS2020
dataset with 494 patients and the BraTS2021 dataset with 1251 patients. Results show that our proposed method
outperforms existing Transformer-based and CNN-based methods using other multi-modal fusion strategies in our
experiments. Specifically, the proposed HCMINet achieves state-of-the-art mean DSC values of 85.33% and 91.09% on the
BraTS2020 online validation dataset and the BraTS2021 local testing dataset, respectively.

## Conclusions

Our proposed method can accurately and automatically segment glioma regions from multi-parametric MR images, which is
beneficial for the quantitative analysis of brain gliomas and helpful for reducing the annotation burden of
neuroradiologists.
