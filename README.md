# 🚁 YOLOv10 vs YOLOv8: Fine-tuning & Efficiency Analysis on VisDrone

![Project Banner](https://img.shields.io/badge/Computer_Vision-2024%2F2025-blue) ![Framework](https://img.shields.io/badge/YOLO-v10%20%7C%20v8-green) ![Dataset](https://img.shields.io/badge/Dataset-VisDrone-orange)

**Author:** Leonardo Vincenzi (ID: 0001056530)  
**Course:** Computer Vision 2024/2025 - Informatica LM  

---

## 📖 Overview
This project presents a comparative analysis between the state-of-the-art **YOLOv10** (End-to-End, NMS-free) and **YOLOv8** architectures, applied to the challenging **VisDrone dataset** (UAV/Drone imagery).

The study aims to verify the efficiency gains of the NMS-free design and analyze model behavior under data scarcity and resolution constraints.

---

## 📊 Key Results

### 1. Efficiency & Latency
YOLOv10s outperforms YOLOv8s in efficiency, achieving comparable accuracy while reducing latency by eliminating the Non-Maximum Suppression (NMS) step.

| Model | mAP50-95 (Test @640px) | Inference Time | Post-Process (NMS) | Total Latency |
| :--- | :---: | :---: | :---: | :---: |
| **YOLOv8s** | 0.194 | 3.1 ms | 1.6 ms | 4.8 ms |
| **YOLOv10s** | **0.195** | 3.2 ms | **0.2 ms** | **3.8 ms** |
| **YOLOv10n** | 0.153 | 1.9 ms | 0.2 ms | 2.2 ms |

> **Key Takeaway:** YOLOv10s reduces total latency by approximately **20%** (~1ms per image) thanks to the End-to-End head.

### 2. The "Resolution Gap"
In UAV scenarios, input resolution is the most critical factor.
* Models trained at standard **640px** perform well up to 1280px inference but degrade significantly at 1600px (>140% upscale).
* **Fine-tuning at 1024px** proved to be the best strategy to maintain accuracy on small objects.

| Configuration | Test Resolution | mAP50-95 | Notes |
| :--- | :---: | :---: | :--- |
| **YOLOv10s (Train 1024px)** | 1600px | **0.2706** | 🏆 Best Performance |
| **YOLOv10s (Train 640px)** | 1280px | 0.2412 | Outperforms v8s (+1.1%) |

---

## 🔬 In-Depth Analysis

### 🧠 The NMS-Free Paradox
Does removing NMS always work? Our experiments show that **deep feature adaptation is mandatory**.
* **Frozen Backbone (Freeze=10):** The NMS-free model performs *worse* than the standard one.
* **Unfrozen Backbone (Freeze=0):** The NMS-free model performs *better* (mAP 0.153 vs 0.131 on Nano).

**Conclusion:** The NMS-free head needs to learn highly discriminative features to avoid duplicate boxes; a generic COCO backbone is not sufficient without fine-tuning.

### 📉 Data Scalability & Overfitting
We analyzed the model's behavior with 25%, 50%, and 100% of the training data.
* **25% Data:** High risk of **overfitting**. While training loss decreased, validation box loss plateaued early (Epoch 53), indicating the model was memorizing geometry rather than generalizing.
* **50% Data:** The "Sweet Spot". It achieved results very close to the full dataset (mAP 0.216 vs 0.241), showing great data efficiency.

---

## 🛠️ Experimental Setup

* **Hardware:** GPU Nvidia P100
* **Optimizer:** AdamW (LR=0.001)
* **Epochs:** 100 (Early Stopping, patience=10)
* **Resolutions:** Trained at 640px and 1024px.

---

## 📝 Conclusions
1.  **Architecture:** YOLOv10s is superior to YOLOv8s for real-time UAV applications, offering higher throughput with equivalent accuracy.
2.  **Strategy:** For drone imagery, increasing training resolution (1024px) yields far better returns than merely changing model architecture.
3.  **Constraints:** The NMS-free approach requires full fine-tuning (unfrozen backbone) to be effective on domain-specific datasets like VisDrone.

---

*Project developed for the Computer Vision course at University of Bologna.*
