# Segmentation-Guided Hybrid Deep Learning Framework for Automated Skin Lesion Classification

**Author:** Wijaya Felicia Angel
**Affiliation:** Department of Data Science, Korea University

---

## Abstract

Accurate classification of skin lesions plays a vital role in the early detection of melanoma and other dermatological conditions. This study proposes a **segmentation-guided hybrid deep learning framework** that integrates convolutional and Transformer architectures for multi-class skin lesion classification using dermatoscopic images from the **HAM10000** dataset.
A **Hybrid CNN–Transformer** model combining **ResNet18** feature extraction with a two-layer Transformer encoder was implemented to jointly capture local texture and global contextual information. Segmentation preprocessing was conducted using **U-Net** and **U-Net++**, generating both **hard-masked** and **soft-blended** lesion images. Furthermore, **Attention Head Pruning** and **Token Pruning** techniques were employed to explore model compression and computational efficiency.
Experimental results demonstrate that the hybrid model trained on **original, unmasked images** achieved the highest validation accuracy (0.844), outperforming its segmented counterparts. This finding highlights that excessive removal of background context may reduce diagnostic cues essential for accurate lesion differentiation. The study concludes that segmentation, when incorporated as an independent preprocessing step, yields limited benefit; its integration through attention-guided or joint optimization strategies holds greater promise for future development.

---

## I. Introduction

Dermatoscopic image analysis is one of the most promising applications of deep learning in dermatology. Early detection of melanoma significantly increases patient survival rates, yet manual examination remains subjective and resource-intensive. Automated lesion classification models can reduce diagnostic bias and support clinicians with consistent, objective predictions.
Recent advancements in **Convolutional Neural Networks (CNNs)** have achieved substantial progress in visual feature extraction, while **Vision Transformers (ViTs)** have introduced mechanisms for global context modeling. However, each has limitations when applied individually: CNNs tend to lose spatial dependencies at larger scales, and Transformers require large-scale datasets to generalize effectively. A hybrid approach can combine the strengths of both architectures.
This research extends previous efforts in medical image classification by developing a **segmentation-enhanced hybrid CNN–Transformer** model. Unlike purely classification-based methods, this framework investigates how **lesion segmentation and contextual preservation** affect performance, as well as whether **lightweight pruning strategies** can maintain diagnostic accuracy while reducing computational demand.

---

## II. Materials and Methods

### A. Dataset

The study utilizes the **HAM10000** (“Human Against Machine with 10000 Training Images”) dataset, comprising over 10,000 dermatoscopic images across seven diagnostic categories: *akiec*, *bcc*, *bkl*, *df*, *mel*, *nv*, and *vasc*. To examine the impact of segmentation, additional lesion masks were generated using pretrained **U-Net** and **U-Net++** architectures sourced from publicly available Kaggle repositories.

### B. Network Architecture

The classification backbone employs a **Hybrid CNN–Transformer** architecture. The convolutional stage is based on **ResNet18**, chosen for its efficient residual connections that facilitate stable training on moderately sized datasets. Its extracted feature maps are reshaped into patch tokens and fed into a **Transformer encoder** comprising two attention layers (8 heads each). The model’s final output is obtained through global average pooling followed by a linear classifier.

For segmentation, **U-Net** was used for two-stage preprocessing, while **U-Net++** enabled joint optimization experiments. Two fusion strategies were tested: **element-wise addition** and **channel concatenation** of original and masked images.

### C. Masking Strategies

Two forms of lesion masking were compared:

1. **Hard Masking** – complete removal of background pixels outside the lesion boundary.
2. **Soft Masking** – contextual blending to preserve peripheral information using the transformation:
   [
   \text{masked} = \text{image} \times (\text{mask} \times 0.7 + 0.3)
   ]
   These strategies were designed to evaluate whether removing non-lesion regions improves classifier focus or inadvertently discards valuable spatial cues.

### D. Model Pruning

To improve inference efficiency, two compression techniques were applied:

* **Attention Head Pruning**, removing less-informative Transformer heads based on gradient-derived Head Importance Scores (HIS), achieving a 23% reduction in FLOPs with minimal accuracy loss (0.3%).
* **Token Pruning**, inspired by *DynamicViT* (Rao et al., 2021), progressively removed low-importance patch tokens during inference (retention ratios: 0.6 → 0.4 → 0.2), leading to a 44% FLOPs reduction with a slight F1 decline (0.86 → 0.84).

---

## III. Results

| Dataset Type | Validation Accuracy | Summary                                                            |
| :----------- | :-----------------: | :----------------------------------------------------------------- |
| Original     |      **0.844**      | Highest accuracy; preserved full spatial and contextual cues.      |
| Hard-Masked  |        0.796        | Accuracy drop due to loss of contextual information.               |
| Soft-Masked  |        0.811        | Partial recovery through blended context but still below baseline. |

**Analysis:**
The hybrid CNN–Transformer achieved optimal performance on unmasked inputs, underscoring the significance of surrounding skin features such as pigmentation distribution, texture continuity, and boundary irregularities.
Both hard and soft masking methods led to performance degradation, suggesting that static segmentation preprocessing disrupts important contextual relationships.

A soft blending approach improved marginally over the hard mask, confirming that partial context preservation is beneficial but insufficient without adaptive attention.
These findings align with recent literature indicating that segmentation contributes more effectively when co-optimized with classification in an **end-to-end** or **attention-guided** framework rather than applied as an isolated preprocessing step.

---

## IV. Discussion

The results affirm that **global contextual cues** play a critical role in dermatological image interpretation. While lesion segmentation enhances visual localization, it can also eliminate diagnostic indicators embedded in the lesion boundary and surrounding tissue. The hybrid model’s consistent performance across unmasked inputs validates the synergy between **convolutional detail extraction** and **Transformer-based contextual reasoning**.
Furthermore, pruning experiments demonstrate that meaningful model compression is feasible without compromising diagnostic performance, supporting the use of hybrid architectures in resource-constrained clinical deployments or mobile screening applications.
Future research should investigate **attention-guided segmentation modules**, **semi-supervised lesion localization**, and **cross-dataset validation** (e.g., PH2, ISIC 2018) to further improve robustness and generalization.

---

## V. Conclusion

This research presents a hybrid CNN–Transformer model for automated skin lesion classification, enhanced through segmentation-guided preprocessing and model pruning. The study reveals that while segmentation offers structural localization benefits, excessive background removal can hinder classification accuracy. The findings advocate for deeper integration between segmentation and attention mechanisms rather than static pre-masking.
By combining feature localization, context awareness, and lightweight optimization, this framework contributes toward developing interpretable and efficient clinical decision-support systems for dermatology. Future work will explore dynamic pruning and self-supervised lesion segmentation to advance toward real-time, explainable AI in medical imaging.

---

## References

1. Rao, Y., Zhao, W., et al. “DynamicViT: Efficient Vision Transformers with Dynamic Token Sparsification,” *Advances in Neural Information Processing Systems*, 2021.
2. Ronneberger, O., Fischer, P., and Brox, T. “U-Net: Convolutional Networks for Biomedical Image Segmentation,” *MICCAI*, 2015.
3. He, K., Zhang, X., Ren, S., and Sun, J. “Deep Residual Learning for Image Recognition,” *CVPR*, 2016.
4. Zare, M., & Pourkazemi, M. “Hybrid Segmentation and Classification for Skin Lesion Detection,” *IEEE Access*, 2023.

---

