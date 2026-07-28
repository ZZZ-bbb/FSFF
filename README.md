# FSFF: Flexible Spatial-Frequency Feature Fusion for UAV-based Semantic Segmentation in Rice Phenotyping

[![Paper](https://img.shields.io/badge/Paper-Smart%20Agricultural%20Technology-green)](https://doi.org/10.1016/j.atech.2025.101641)
[![License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Python](https://img.shields.io/badge/Python-3.8%2B-yellow.svg)](https://www.python.org/)
[![PyTorch](https://img.shields.io/badge/PyTorch-1.10%2B-red.svg)](https://pytorch.org/)

> **Official implementation** of the paper:
> **"Flexible spatial-frequency feature fusion for UAV-based semantic segmentation in rice phenotyping"**
> *Smart Agricultural Technology, 2025, 12: 101641*
>
> Zibin Zhou<sup>1</sup>, Xuan Wei<sup>1</sup>, Huasheng Huang\*, Jiajun Zhuang, Peng Zhang, Tao Guo, Hong Liu, Zhiping Tan, Yu Tang\*
>
> <sup>1</sup> Co-first authors. \* Corresponding authors.

📄 [Paper (ScienceDirect)](https://doi.org/10.1016/j.atech.2025.101641)

---

## 🌾 Overview

UAV-based semantic segmentation offers new insights to accelerate breeding better varieties in rice breeding applications. However, the **morphological similarity** and **mutual occlusion** between panicles and leaves still pose severe challenges for efficient rice phenotyping.

We propose a **Flexible Spatial-Frequency Feature Fusion (FSFF)** method for high-throughput UAV-based semantic segmentation. FSFF achieves **state-of-the-art performance** on our rice phenotyping dataset with the **fastest inference speed (0.7311 ms/image)** among all compared spatial-frequency fusion methods, and generalizes well to public benchmarks (PlantVillage) and practical breeding applications (yield & AGB prediction).

![framework](assets/framework.png)
*Figure 1. Overall workflow of the proposed FSFF framework.*

## ✨ Highlights

- **Learnable Frequency Feature Extraction (LFFE)** — replaces fixed-kernel frequency filtering (DFRF-based) with adaptive depthwise separable convolution frequency filters, building learnable frequency awareness to distinguish morphologically similar panicles and leaves.
- **Adaptive Spatial Context Enhancement (ASCE)** — employs deformable convolution to adaptively focus on salient spatial regions, enhancing boundary delineation and mitigating mutual occlusion.
- **Hierarchical Feature Fusion (HFF)** — integrates spatial and frequency features via element-wise addition, LayerNorm, MLP transformation and residual connection, preserving inter-class variation and structural details.
- **Lightweight & fast** — depthwise separable design yields the best inference speed (0.7311 ms/image), suitable for high-throughput UAV phenotyping.
- **Robust to illumination changes** — minimal mIoU fluctuation (range = 0.10) under brightness/contrast color jitters, significantly more stable than counterparts.
- **Practical breeding value** — downstream yield prediction (R² = 0.8706, LOOCV) and AGB prediction (R² = 0.9493, LOOCV) via handcrafted features + linear regression on segmentation results.

## 🏗️ Method

The FSFF framework consists of three key components:

### 1. LFFE — Learnable Frequency Feature Extraction

Inspired by the Convolution Theorem (convolution in the spatial domain equals filtering in the frequency domain), LFFE learns frequency responses instead of using fixed kernels:

| Sub-module | Full name | Function |
|---|---|---|
| **MSE** | Multi-Scale Enhancement | Combines upsampled (low-frequency) and skip-connection (high-frequency) features across network layers |
| **CWE** | Channel-Wise Enhancement | Adaptively calibrates channel-wise correlation with a lightweight sigmoid-gated attention |
| **ADFF** | Adaptive Depthwise separable convolution Frequency Filters | Depthwise separable convolutions acting as learnable bandpass filters, drastically reducing computation |
| **ARFF** | Adaptive Residual Frequency Filter | Residual structure with learnable α/β to dynamically modulate frequency response during training |

### 2. ASCE — Adaptive Spatial Context Enhancement

Deformable convolution with learned offset fields dynamically adjusts the receptive field according to input features, capturing local context of irregular panicle/leaf boundaries under occlusion.

### 3. HFF — Hierarchical Feature Fusion

Spatial-frequency features are fused by element-wise addition → LayerNorm → MLP → residual connection, replacing the conventional concatenation + 1×1 convolution to better preserve inter-class variation and structural details.

## 📊 Results

### Comparison with spatial-feature segmentation methods (test set)

| Method | mIoU ↑ | Pixel Acc ↑ | SSIM ↑ |
|---|---|---|---|
| FCN | 0.6318 | 0.8411 | 0.7172 |
| DeepLabV3 | 0.6977 | 0.8714 | 0.7618 |
| U-Net | 0.7123 | 0.8625 | 0.7713 |
| SegNet | 0.6820 | 0.8615 | 0.7590 |
| SegFormer | 0.6299 | 0.8496 | 0.7425 |
| Trans-Unet | 0.6504 | 0.8579 | 0.7305 |
| Swin-Unet | 0.6753 | 0.8611 | 0.7527 |
| UNet++ | 0.7196 | 0.8795 | 0.7841 |
| **FSFF (ours)** | **0.7335** | **0.8883** | **0.7947** |
| **FSFF-CRF (ours)** | **0.7478** | **0.9080** | **0.8085** |

### Comparison with spatial-frequency feature fusion methods

| Method | Test mIoU ↑ | Test Pixel Acc ↑ | Test SSIM ↑ | Inference Time (ms) ↓ |
|---|---|---|---|---|
| FSANet | 0.6910 | 0.8653 | 0.7724 | 1.8857 |
| FDEG-Net | 0.6714 | 0.8597 | 0.7754 | 0.9372 |
| CDFW | 0.6976 | 0.8708 | 0.7821 | 67.8855 |
| FFS-Net | 0.7123 | 0.8803 | 0.7838 | 64.9685 |
| **FSFF (ours)** | **0.7335** | **0.8883** | **0.7947** | **0.7311** |
| **FSFF-CRF (ours)** | **0.7478** | **0.9080** | **0.8085** | 3.2229 |

### Ablation study (LFFE & ASCE)

| LFFE | ASCE | Test mIoU | Test Pixel Acc | Test SSIM |
|---|---|---|---|---|
| ✗ | ✗ | 0.7123 | 0.8625 | 0.7713 |
| ✓ | ✗ | 0.7142 | 0.8800 | 0.7869 |
| ✗ | ✓ | 0.7240 | 0.8839 | 0.7923 |
| ✓ | ✓ | **0.7335** | **0.8883** | **0.7947** |

### Generalization on PlantVillage (rice leaf disease segmentation)

| Method | mIoU ↑ | Recall ↑ | Precision ↑ |
|---|---|---|---|
| SegFormer | 0.782 | 0.619 | 0.889 |
| Swin Transformer | 0.812 | 0.721 | 0.841 |
| SETR | 0.824 | 0.739 | 0.853 |
| AISOA-SSformer | 0.831 | 0.765 | 0.892 |
| **FSFF (ours)** | **0.891** | **0.872** | **0.896** |

### Downstream breeding applications (14 rice varieties)

| Trait | LOOCV R² | 10-fold CV R² |
|---|---|---|
| Yield | 0.8706 | 0.8510 |
| AGB | 0.9493 | 0.9439 |
