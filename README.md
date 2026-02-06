# Satellite Imagery Analysis: Object Detection & Segmentation

This project implements a high-performance computer vision pipeline designed to detect and segment aircraft in satellite imagery. By exploring architectures like **Faster R-CNN**, **U-Net**, and **Mask R-CNN**, the project highlights the critical role of hyperparameter tuning and architectural modifications in handling high-resolution aerial data.

---

## 🛰️ Project Overview

The objective was to optimize models for three primary tasks on satellite datasets:

1.  **Object Detection:** Accurate localization of planes using bounding boxes.
2.  **Semantic Segmentation:** Pixel-level classification for distinguishing planes from the background.
3.  **Instance Segmentation:** Differentiating between individual plane instances.

---

## 🎯 Task 1: Object Detection (Faster R-CNN)

I utilized the **Faster R-CNN** architecture, which provided a robust balance between detection accuracy and inference speed for satellite imagery.

### Key Optimization: ROI Head Tuning

The most significant performance improvement resulted from tuning the **ROI Head** configuration. By increasing the `BATCH_SIZE_PER_IMAGE` to **4096** (the limit of my local VRAM), the model achieved a significantly higher chance of detecting objects.

| Parameter           | Value  | Rationale                                          |
| :------------------ | :----- | :------------------------------------------------- |
| **Learning Rate**   | 0.001  | Best converging rate at high speed.                |
| **ROI Batch Size**  | 4096   | Essential for improving $AP_{50}$.                 |
| **Weight Decay**    | 0.0005 | Implemented to prevent overfitting.                |
| **Backbone Freeze** | 2      | Ensures wings are not detected as separate planes. |

**Results:**

- **Training $AP_{50}$:** Increased from 55 to 63.
- **Kaggle Score:** Improved from 0.28 to 0.33.

---

## 🧬 Task 2: Semantic Segmentation (U-Net)

For pixel-level segmentation of 128x128 satellite images, I developed a custom **U-Net** architecture.

### Architectural Refinements

The model follows an encoder-decoder structure with skip connections to combine low-level and high-level semantic features.

- **Batch Normalization:** Added for stable training.
- **ReLU Activations:** Used to preserve pixel intensity information.
- **Bilinear Upsampling:** Balanced smoothness and detail during reconstruction.
- **Adaptive Learning Rate:** Used an Adam Optimizer with a `ReduceLROnPlateau` scheduler to ensure smooth loss convergence.

**Performance Comparison:**

- **Custom U-Net:** Achieved a 0.37 mean IoU score.
- **Pre-trained U-Net (ResNet-34):** Achieved a 0.45 mean IoU score.

---

## 🎭 Task 3 & 4: Instance Segmentation & Evaluation

The final phase utilized a combined model for **Instance Segmentation**, achieving a top Kaggle score of **0.43695**.

### Mask R-CNN Comparison

I evaluated a **Mask R-CNN** model (trained for 2000 epochs) as a baseline comparison to my optimized pipeline.

- **Overfitting:** Mask R-CNN was more robust, correctly identifying ground markings (T-shapes) as noise rather than planes.
- **Speed:** My combined model was **5–10 times faster** in inference than Mask R-CNN.
- **Conclusion:** While Mask R-CNN is potentially more accurate with extensive tuning, my optimized pipeline is the better choice for speed-focused tasks.

---

## 🛠️ Setup & Data Augmentation

### Data Augmentation Strategy

To improve generalization and handle imbalanced object sizes, I implemented the following:

- **Random Crop:** Enabled with a size of `[0.5, 0.5]`.
- **Horizontal Flip:** Applied to the input images.
- **Variable Sampling:** Training sizes chosen between `[800, 1000, 1200, 1400]`.

---

## 📈 Final Results Summary

- **Best Kaggle Score:** 0.43695.
- **Validation $AP_{50}$:** 82.
- **Segmentation IoU:** 45.

> **Note on Model Limitations:** While the model performs well, it still faces challenges in extremely dense environments where planes are connected or adjacent.

---

**Author:** Lei Shi
