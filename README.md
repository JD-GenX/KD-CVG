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

Creative Generation (CG) leverages generative models to automatically produce advertising content that highlights product features, and it has been a significant focus of recent research. However, while CG has advanced considerably, most efforts have concentrated on generating advertising text and images, leaving **Creative Video Generation (CVG)** relatively underexplored. This gap is largely due to two major challenges faced by Text-to-Video (T2V) models:

- **(a) Ambiguous semantic alignment**, where models struggle to accurately correlate product selling points with creative video content.
- **(b) Inadequate motion adaptability**, resulting in unrealistic movements and distortions.

To address these challenges, we develop a comprehensive **Advertising Creative Knowledge Base (ACKB)** as a foundational resource and propose a knowledge-driven approach (**KD-CVG**) to overcome the knowledge limitations of existing models. KD-CVG consists of two primary modules: **Semantic-Aware Retrieval (SAR)** and **Multimodal Knowledge Reference (MKR)**.

- **SAR** utilizes the semantic awareness of graph attention networks and reinforcement learning feedback to enhance the model's comprehension of the connections between selling points and creative videos.
- Building on this, **MKR** incorporates semantic and motion priors into the T2V model to address existing knowledge gaps.

Extensive experiments have demonstrated KD-CVG's superior performance in achieving semantic alignment and motion adaptability, validating its effectiveness over other state-of-the-art methods.

---

## Challenges

<p align="center">
  <img src="assets/challenges.png" alt="Challenges in ACV generation" width="90%">
</p>

Illustration of challenges in T2V model generation of Advertising Creative Videos (ACVs) for e-commerce scenarios:

- **(a)** Low semantic correlation between selling point information and video content leads to alignment issues.
- **(b)** General T2V models, lacking e-commerce-specific motion priors, often display non-physical movements and motion deformations.

---

## Method Overview

<p align="center">
  <img src="assets/framework.png" alt="KD-CVG Framework" width="95%">
</p>

<p align="center"><i>Overview of the two core modules in the KD-CVG framework: Semantic-Aware Retrieval (SAR) and Multimodal Knowledge Reference (MKR).</i></p>

### 1. Advertising Creative Knowledge Base (ACKB)

We construct ACKB, a high-quality knowledge base tailored for e-commerce creative video generation. Starting from 58K advertising videos crawled from a major e-commerce platform, we apply a multi-stage pipeline:

1. **Quality filtering** following the pipeline of [xGen-VideoSyn](https://arxiv.org/abs/2408.12590) to remove low-quality clips.
2. **Selling-point extraction** with Qwen2-VL from textual overlays in the videos.
3. **Watermark / text removal** with ProPainter to ensure clean visual content.
4. **Script generation** with CogVLM2 to produce a general script description for every clip, providing direct semantic references.
5. **Manual quality review** by reviewers with 5+ years of e-commerce experience.

The final ACKB contains **~10K selling-point / video pairs** covering **377 product categories** (e.g., toothbrushes, facial masks, shampoos). Videos average ~3 seconds with resolutions ranging from 240p to 2120p.

| Dataset     | Domain     | #Videos | Avg. Duration | Avg. Caption Len | Resolution   |
|-------------|------------|---------|---------------|------------------|--------------|
| MSVD        | Open       | 1,970   | ~10s          | 8.7 words        | -            |
| MSR-VTT     | Open       | 10K     | ~15s          | 9.3 words        | 240p         |
| DiDeMo      | Flickr     | 27K     | ~7s           | 8.0 words        | -            |
| **ACKB (Ours)** | **E-commerce** | **10K** | **~3s** | **8.8 words** | **240p – 2120p** |

### 2. Semantic-Aware Retrieval (SAR)

SAR tackles **ambiguous semantic alignment** by modeling the semantic space as a graph, where each node is a CLIP-encoded selling-point text and edges encode pairwise semantic relationships. We propose the **Semantic Correlation Graph Attention Network (SC-GAT)** that computes context-aware attention:

```math
\alpha_{ij} = \text{softmax}\left(\frac{(W_q F_{p_i})^\top (W_k F_{p_j})}{\sqrt{d}}\right)
```

The top-*k* candidates retrieved by SC-GAT are optimized through **policy-gradient reinforcement learning** with the CIDEr reward:

```math
\nabla_\phi J(\phi) = \mathbb{E}_{\pi_\phi}\!\left[R(\hat{G})\,\nabla_\phi \log \pi_\phi(\hat{G})\right], \quad R(\hat{G}) = \text{CIDEr}(\hat{G}, G)
```

### 3. Multimodal Knowledge Reference (MKR)

MKR consumes SAR's top-*k* references and runs a two-stage hierarchical generation pipeline:

**Progressive Script Generation.** A three-step prompting workflow guided by the LLM:

1. **Semantic Structure Parsing** – decompose the top-ranked reference script into `{subject, scene, motion}` templates.
2. **Motion-preserved Adaptation** – keep the motion template, adapt subject/scene to the new product.
3. **Context-aware Synthesis** – fuse motion prior, adapted components, and the broader semantic context into the final script.

**Motion Prior Distillation.** We encode differential motion vectors in latent space and introduce a motion distillation loss:

```math
\mathcal{L}_{\text{md}} = \mathbb{E}_{t,n}\!\left[\,\|\Delta M^n - (v_t^{n+1} - v_t^n)\|_2^2\,\right]
```

The distilled priors are injected into the temporal attention blocks of the ST-DiT backbone via a lightweight **MR-LoRA** module. During inference we additionally apply **Rectified Flow Inversion (RFI)** on the reference video to obtain structurally aligned initial noise, enabling semantic fidelity *and* physically plausible motion.

---

## Results

<p align="center">
  <img src="assets/comparison.png" alt="Qualitative comparison" width="95%">
</p>

Qualitative comparison with state-of-the-art video generation models. KD-CVG produces videos with noticeably better motion realism, temporal smoothness, and semantic alignment with the target selling points.

### Quantitative Comparison

| Method              | Textual Alignment | Temporal Consistency | Dynamic Degree | Motion Smoothness | Min-Max Score |
|---------------------|:-----------------:|:--------------------:|:--------------:|:-----------------:|:-------------:|
| Show-1              | **31.34%**        | 96.83%               | 44.44%         | 98.04%            | 55.66%        |
| VideoCrafter2       | 30.43%            | 97.03%               | **88.89%**     | 91.91%            | 44.69%        |
| Open-Sora           | 28.84%            | 97.94%               | 22.22%         | 98.92%            | 47.06%        |
| w/. MR-LoRA         | 29.45%            | **98.15%**           | 16.00%         | **99.24%**        | 56.10%        |
| w/. RFI             | 30.59%            | 97.55%               | 60.00%         | 97.52%            | _65.36%_      |
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

We publicly release the Advertising Creative Knowledge Base (ACKB), containing approximately **10K** selling-point / ACV pairs with associated creative scripts and metadata.

- **Download link:** <https://3.cn/1186-864O>
- **Access password:** `qj2w7y`

> Please use the dataset for academic / research purposes only and cite our paper if you use ACKB in your work.

---

## Implementation Details

- **Backbone:** [Open-Sora v1.2](https://github.com/hpcaitech/Open-Sora)
- **LLM:** GPT-4
- **MR-LoRA:** applied on the query projections of the self-attention layers in T-DiT-B, rank `r = 128`
- **Optimizer:** Adam, learning rate `1e-4`, 400 training steps
- **Hardware:** single NVIDIA H800 GPU
- **Inference:** 30 inversion / 30 denoising steps, classifier-free guidance scale `7.0`, reference intensity `β ∈ [0.6, 0.8]`

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

## Acknowledgements

This work builds upon several excellent open-source projects, including [Open-Sora](https://github.com/hpcaitech/Open-Sora), [VMC](https://github.com/HyeonHo99/Video-Motion-Customization), [ProPainter](https://github.com/sczhou/ProPainter), [CogVLM2](https://github.com/THUDM/CogVLM2), and [Qwen2-VL](https://github.com/QwenLM/Qwen2-VL). We thank all authors for their contributions to the community.

---
