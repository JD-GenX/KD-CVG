# KD-CVG: A Knowledge-Driven Approach for Creative Video Generation

<p align="center">
  <b>ICASSP 2026</b>
</p>

<p align="center">
  Linkai Liu<sup>†</sup> &nbsp;·&nbsp; Wei Feng<sup>★</sup> &nbsp;·&nbsp; Xi Zhao<sup>★</sup> &nbsp;·&nbsp; Shen Zhang<sup>★</sup> &nbsp;·&nbsp; Xingye Chen<sup>★</sup> &nbsp;·&nbsp; Zheng Zhang<sup>★</sup> <br>
  Jingjing Lv<sup>★</sup> &nbsp;·&nbsp; Junjie Shen<sup>★</sup> &nbsp;·&nbsp; Ching Law<sup>★</sup> &nbsp;·&nbsp; Yuchen Zhou<sup>†</sup> &nbsp;·&nbsp; Zipeng Guo<sup>†</sup> &nbsp;·&nbsp; Chao Gou<sup>†‡</sup>
</p>

<p align="center">
  <sup>†</sup> Sun Yat-sen University &nbsp;&nbsp;&nbsp; <sup>★</sup> JD.COM <br>
  <sup>‡</sup> Corresponding author
</p>

<p align="center">
  <a href="https://kdcvg.github.io/KDCVG/"><img src="https://img.shields.io/badge/Project-Page-blue" alt="Project Page"></a>
  <img src="https://img.shields.io/badge/Conference-ICASSP%202026-red" alt="ICASSP 2026">
  <img src="https://img.shields.io/badge/License-MIT-green" alt="License">
</p>

---

## Abstract

Creative Generation (CG) leverages generative models to automatically produce advertising content that highlights product features, and it has been a significant focus of recent research. However, while CG has advanced considerably, most efforts have concentrated on generating advertising text and images, leaving **Creative Video Generation (CVG)** relatively underexplored. This gap is largely due to two major challenges faced by Text-to-Video (T2V) models: **(a) ambiguous semantic alignment**, where models struggle to accurately correlate product selling points with creative video content, and **(b) inadequate motion adaptability**, resulting in unrealistic movements and distortions.

To address these challenges, we develop a comprehensive **Advertising Creative Knowledge Base (ACKB)** as a foundational resource and propose a knowledge-driven approach (**KD-CVG**) to overcome the knowledge limitations of existing models. KD-CVG consists of two primary modules: **Semantic-Aware Retrieval (SAR)** and **Multimodal Knowledge Reference (MKR)**. SAR utilizes the semantic awareness of graph attention networks and reinforcement learning feedback to enhance the model's comprehension of the connections between selling points and creative videos. Building on this, MKR incorporates semantic and motion priors into the T2V model to address existing knowledge gaps. Extensive experiments have demonstrated KD-CVG's superior performance in achieving semantic alignment and motion adaptability, validating its effectiveness over other state-of-the-art methods.

---

## Method

<p align="center">
  <img src="figures/KD-CVG.png" alt="KD-CVG Framework" width="95%">
</p>

<p align="center"><i>Overview of the two core modules in the KD-CVG framework: Semantic-Aware Retrieval (SAR) and Multimodal Knowledge Reference (MKR).</i></p>

---

## Results

<p align="center">
  <img src="figures/comself.png" alt="Qualitative comparison" width="95%">
</p>

<p align="center"><i>Qualitative comparison with state-of-the-art video generation models.</i></p>

### Quantitative Comparison

| Method              | Textual Alignment | Temporal Consistency | Dynamic Degree | Motion Smoothness | Min-Max Score |
|---------------------|:-----------------:|:--------------------:|:--------------:|:-----------------:|:-------------:|
| Show-1              | **31.34%**        | 96.83%               | 44.44%         | 98.04%            | 55.66%        |
| VideoCrafter2       | 30.43%            | 97.03%               | **88.89%**     | 91.91%            | 44.69%        |
| Open-Sora           | 28.84%            | 97.94%               | 22.22%         | 98.92%            | 47.06%        |
| **KD-CVG (Ours)**   | _30.68%_          | _97.95%_             | _72.00%_       | _98.65%_          | **81.80%**    |

### User Study & Inference Cost

| Method             | Text Alignment↑ | Video Quality↑ | Motion Regularity↑ | Highlight Degree↑ | Time Cost↓ |
|--------------------|:---------------:|:--------------:|:------------------:|:-----------------:|:----------:|
| Show-1             | _3.70_          | 3.23           | _3.44_             | _3.59_            | 3692s      |
| VideoCrafter2      | 3.63            | **3.81**       | 2.26               | 3.26              | 161s       |
| Open-Sora          | 3.26            | 3.07           | 2.63               | 3.30              | **52s**    |
| **KD-CVG (Ours)**  | **3.74**        | _3.70_         | **3.81**           | **3.89**          | _102s_     |

---

## Dataset (ACKB)

We publicly release the Advertising Creative Knowledge Base (ACKB), containing approximately **10K** selling-point / ACV pairs with associated creative scripts and metadata, covering **377 daily product categories** (e.g., toothbrushes, facial masks, shampoos).

- **Download link:** <https://3.cn/1186-864O>
- **Access password:** `qj2w7y`

> Please use the dataset for academic / research purposes only and cite our paper if you use ACKB in your work.

---

## Citation

If you find this work useful, please consider citing:

```bibtex
@inproceedings{liu2026kdcvg,
  title     = {KD-CVG: A Knowledge-Driven Approach for Creative Video Generation},
  author    = {Liu, Linkai and Feng, Wei and Zhao, Xi and Zhang, Shen and Chen, Xingye and
               Zhang, Zheng and Lv, Jingjing and Shen, Junjie and Law, Ching and
               Zhou, Yuchen and Guo, Zipeng and Gou, Chao},
  booktitle = {IEEE International Conference on Acoustics, Speech and Signal Processing (ICASSP)},
  year      = {2026}
}
```

---
