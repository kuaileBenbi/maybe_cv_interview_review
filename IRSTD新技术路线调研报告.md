# IRSTD 新技术路线调研报告：Transformer / Mamba / 频域方法

> 调研时间：2026-05-07
> 背景：SmallRegionNet 为纯 CNN 架构，探索引入 Transformer 或更新技术提升红外小目标检测性能

---

## 一、Mamba / State Space Model（线性复杂度的序列建模）

这是 2024-2025 年 IRSTD 领域最火的新方向，核心优势是 O(n) 复杂度替代 Transformer 的 O(n²)。

### 1. MiM-ISTD: Mamba-in-Mamba for Efficient Infrared Small Target Detection

- **发表：IEEE TGRS 2024**（地球科学遥感领域顶刊）
- **链接：** [arXiv 2403.02148](https://arxiv.org/abs/2403.02148) | [GitHub 代码](https://github.com/txchen-USTC/MiM-ISTD)
- **核心思路：** 嵌套式 Mamba 结构——外层 Mamba 建模全局依赖，内层 Mamba 建模局部 patch 内部关系。把图像分为"视觉句子"（patch）和"视觉单词"（pixel），两级扫描。
- **关键结果：** 比 SOTA 快 **10 倍**，GPU 显存降低 **73.4%**（2048×2048 图像），精度保持 SOTA 水平。

### 2. IRMamba: Pixel Difference Mamba with Layer Restoration for IRSTD

- **发表：AAAI 2025**（AI 顶会）
- **链接：** [AAAI 论文](https://ojs.aaai.org/index.php/AAAI/article/view/33085/35240) | [ResearchGate](https://www.researchgate.net/publication/390722572)
- **核心思路：** 在 Mamba 序列建模前引入 **像素差分操作**（Pixel Difference），增强对小目标边缘的敏感性。配合 Layer Restoration 机制防止深层特征退化。
- **亮点：** 差分操作天然增强局部对比度，与 SmallRegionNet 的 Local Anomaly Block 思路有异曲同工之妙。

### 3. UniMamba: A Unified CNN-Mamba Model for IRSTD

- **发表：** ACM/Springer 2024
- **链接：** [Springer](https://www.springerprofessional.de/en/unimamba-a-unified-cnn-mamba-model-for-infrared-small-target-det/51256720)
- **核心思路：** CNN 提取局部特征 + Mamba 建模全局依赖的融合架构。与"保留 CNN 编码器 + 换 Transformer/Mamba Neck"的思路最接近。

### 4. 其他 Mamba 相关工作

| 论文 | 发表 | 核心思路 |
|------|------|----------|
| SAMamba | arXiv 2025 | 自适应状态空间建模 + 层次化视觉 |
| LMDNet | Springer 2025 | 轻量级 Mamba + 差分卷积 |
| EAMNet | Elsevier 2025 | 高效自适应 Mamba 网络 |
| CP-SSM | ScienceDirect 2025 | 因果预测状态空间模型，引导注意力 |

---

## 二、Frequency Domain / 小波变换（频域增强检测）

核心思想：小目标在高频子带更显著，频域处理可以天然增强信噪比。

### 5. Infrared Small Target Detection with Wavelet Attentions

- **发表：** ACM 2024/2025
- **链接：** [ACM Digital Library](https://dl.acm.org/doi/full/10.1145/3718491.3718608)
- **核心思路：** 用 2-D 离散小波变换（DWT）提取高频分量，结合注意力机制增强目标特征。小波分解天然实现无损多尺度分析。

### 6. FSCNet: Feature Synthesis with Wavelet Coefficients for ISTD

- **发表：Infrared Physics & Technology 2025**（红外领域核心期刊）
- **链接：** [ScienceDirect](https://www.sciencedirect.com/science/article/abs/pii/S1350449525001185)
- **核心思路：** 利用小波系数进行特征合成（Feature Synthesis），在频域直接构建目标检测特征。

### 7. DWTFreqNet: Wavelet-Driven Frequency Matching + Saliency-Difference Optimization

- **发表：** 2024/2025
- **链接：** [ResearchGate](https://www.researchgate.net/publication/395431238_DWTFreqNet_Infrared_Small_Target_Detection_via_Wavelet-Driven_Frequency_Matching_and_Saliency-Difference_Optimization)
- **核心思路：** 小波驱动的频率匹配 + 显著性差异优化损失函数，同时从特征提取和训练目标两个层面利用频域信息。

### 8. Wavelet Steered Network for Efficient IRSTD

- **发表：Infrared Physics & Technology 2025**
- **链接：** [ScienceDirect](https://www.sciencedirect.com/science/article/abs/pii/S1350449525001434)
- **核心思路：** 用小波变换替代池化实现 **无损下/上采样**，配合 frequency-aware attention 跨通道分离目标和背景。这一思路可以直接嵌入现有 U-Net 型编解码器。

---

## 三、Transformer 及大核卷积（全局建模新方案）

### 9. SCTransNet: Spatial-Channel Cross Transformer Network

- **发表：IEEE TIP 2024**（图像处理顶刊）
- **核心思路：** 空间-通道交叉 Transformer，在空间维度和通道维度之间做 cross-attention，比纯空间自注意力更适合小目标（通道间关系蕴含语义信息）。

### 10. SAIST: Segment Any Infrared Small Target

- **发表：CVPR 2025**（计算机视觉顶会）
- **链接：** [CVPR 2025 Poster](https://cvpr.thecvf.com/virtual/2025/poster/32729)
- **核心思路：** 基于 SAM（Segment Anything Model）的 foundation model 思路，用基础模型指导红外小目标检测。代表了 IRSTD 领域的 foundation model 趋势。

### 11. Pinwheel-Shaped Convolution + Scale-based Dynamic Loss

- **发表：AAAI 2025**
- **链接：** [AAAI 论文](https://ojs.aaai.org/index.php/AAAI/article/view/32996)
- **核心思路：** 设计 **风车形卷积核**（Pinwheel-shaped Conv）替代标准卷积，用非对称大感受野替代传统方形核，配合尺度动态损失函数。属于大核卷积的创新变体。

### 12. Dynamic High-Frequency Convolution for IRSTD

- **发表：arXiv 2025**
- **链接：** [arXiv 2602.02969](https://arxiv.org/abs/2602.02969)
- **核心思路：** 动态高频卷积——自适应提取高频分量，将频域思想和卷积操作统一到一个可学习模块中。

---

## 四、综合对比

| 方向 | 代表论文 | 发表级别 | 核心优势 | 与 SmallRegionNet 的结合点 |
|------|----------|----------|----------|---------------------------|
| **Mamba/SSM** | MiM-ISTD, IRMamba | TGRS, AAAI | O(n)复杂度，10x加速 | 替换编码器或融合阶段 |
| **频域/小波** | Wavelet Attentions, FSCNet | ACM, Infrared Phys. | 高频增强信噪比 | 替换池化/下采样，或增加频域分支 |
| **Transformer** | SCTransNet, SAIST | TIP, CVPR | 全局建模能力强 | 作为 Neck 的 cross-attention |
| **大核卷积** | Pinwheel Conv | AAAI | 大感受野无需自注意力 | 替换编码器中的 3x3 卷积 |

### 最值得深入阅读的 3 篇（按与 SmallRegionNet 相关性排序）

1. **IRMamba (AAAI 2025)** — 像素差分 + Mamba 的思路与 LAB 模块高度互补，novelty 足
2. **MiM-ISTD (TGRS 2024)** — Mamba 在 IRSTD 的开山之作，有完整代码可参考
3. **Wavelet Steered Network** — 小波无损上下采样可直接嵌入现有 FPN 架构，改动最小

---

## 五、参考资源

- [Awesome Infrared Small Targets (GitHub 综合列表)](https://github.com/Tianfang-Zhang/awesome-infrared-small-targets) — 持续更新的 IRSTD 论文/代码/数据集索引
- [MiM-ISTD 官方代码](https://github.com/txchen-USTC/MiM-ISTD) — 可直接参考的 Mamba IRSTD 实现
