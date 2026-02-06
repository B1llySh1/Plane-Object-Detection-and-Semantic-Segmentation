# Satellite Imagery Analysis: Object Detection & Segmentation

[cite_start]This project implements a high-performance computer vision pipeline designed to detect and segment aircraft in satellite imagery[cite: 5, 6, 46]. [cite_start]By exploring architectures like **Faster R-CNN**, **U-Net**, and **Mask R-CNN**, the project highlights the critical role of hyperparameter tuning and architectural modifications in handling high-resolution aerial data[cite: 7, 33, 49].

---

## 🛰️ Project Overview

The objective was to optimize models for three primary tasks on satellite datasets:

1.  [cite_start]**Object Detection:** Accurate localization of planes using bounding boxes[cite: 1].
2.  [cite_start]**Semantic Segmentation:** Pixel-level classification for distinguishing planes from the background[cite: 1, 47].
3.  [cite_start]**Instance Segmentation:** Differentiating between individual plane instances[cite: 2].

---

## 🎯 Task 1: Object Detection (Faster R-CNN)

[cite_start]I utilized the **Faster R-CNN** architecture, which provided a robust balance between detection accuracy and inference speed for satellite imagery[cite: 5, 6].

### Key Optimization: ROI Head Tuning

[cite_start]The most significant performance improvement resulted from tuning the **ROI Head** configuration[cite: 7, 34]. [cite_start]By increasing the `BATCH_SIZE_PER_IMAGE` to **4096** (the limit of my local VRAM), the model achieved a significantly higher chance of detecting objects[cite: 19, 36].

| Parameter           | Value  | Rationale                                                                    |
| :------------------ | :----- | :--------------------------------------------------------------------------- |
| **Learning Rate**   | 0.001  | [cite_start]Best convergence rate at high speed[cite: 10, 11].               |
| **ROI Batch Size**  | 4096   | [cite_start]Essential for improving $AP_{50}$[cite: 19, 34].                 |
| **Weight Decay**    | 0.0005 | [cite_start]Implemented to prevent overfitting[cite: 17].                    |
| **Backbone Freeze** | 2      | [cite_start]Ensures wings are not detected as separate planes[cite: 24, 25]. |

**Results:**

- [cite_start]**Training $AP_{50}$:** Increased from 55 to 63[cite: 21].
- [cite_start]**Kaggle Score:** Improved from 0.28 to 0.33[cite: 21].

---

## 🧬 Task 2: Semantic Segmentation (U-Net)

[cite_start]For pixel-level segmentation of 128x128 satellite images, I developed a custom **U-Net** architecture[cite: 46, 47, 51].

### Architectural Refinements

[cite_start]The model follows an encoder-decoder structure with skip connections to combine low-level and high-level semantic features[cite: 48].

- [cite_start]**Batch Normalization:** Added for stable training[cite: 49].
- [cite_start]**ReLU Activations:** Used to preserve pixel intensity information[cite: 49].
- [cite_start]**Bilinear Upsampling:** Balanced smoothness and detail during reconstruction[cite: 49].
- [cite_start]**Adaptive Learning Rate:** Used an Adam Optimizer with a `ReduceLROnPlateau` scheduler to ensure smooth loss convergence[cite: 59, 61].

**Performance Comparison:**

- [cite_start]**Custom U-Net:** Achieved a 0.37 mean IoU score[cite: 52].
- [cite_start]**Pre-trained U-Net (ResNet-34):** Achieved a 0.45 mean IoU score[cite: 52].

---

## 🎭 Task 3 & 4: Instance Segmentation & Evaluation

[cite_start]The final phase utilized a combined model for **Instance Segmentation**, achieving a top Kaggle score of **0.43695**[cite: 75].

### Mask R-CNN Comparison

[cite_start]I evaluated a **Mask R-CNN** model (trained for 2000 epochs) as a baseline comparison to my optimized pipeline[cite: 79].

- [cite_start]**Overfitting:** Mask R-CNN was more robust, correctly identifying ground markings (T-shapes) as noise rather than planes[cite: 81].
- [cite_start]**Speed:** My combined model was **5–10 times faster** in inference than Mask R-CNN[cite: 84].
- [cite_start]**Conclusion:** While Mask R-CNN is potentially more accurate with extensive tuning, my optimized pipeline is the better choice for speed-focused tasks[cite: 85, 87].

---

## 🛠️ Setup & Data Augmentation

### Data Augmentation Strategy

[cite_start]To improve generalization and handle imbalanced object sizes, I implemented the following[cite: 26, 31]:

- [cite_start]**Random Crop:** Enabled with a size of `[0.5, 0.5]`[cite: 27, 28].
- [cite_start]**Horizontal Flip:** Applied to the input images[cite: 29].
- [cite_start]**Variable Sampling:** Training sizes chosen between `[800, 1000, 1200, 1400]`[cite: 30, 31].

---

## 📈 Final Results Summary

- [cite_start]**Best Kaggle Score:** 0.43695[cite: 75].
- [cite_start]**Validation $AP_{50}$:** 82[cite: 80].
- [cite_start]**Segmentation IoU:** 45[cite: 80].

> [cite_start]**Note on Model Limitations:** While the model performs well, it still faces challenges in extremely dense environments where planes are connected or adjacent[cite: 44, 82].

---

[cite_start]**Author:** Lei Shi [cite: 3, 74]
