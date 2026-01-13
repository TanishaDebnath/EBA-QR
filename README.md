
# EBA-QR: An Entropy-Based Adaptive Quantum Image Representation Framework

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1tUTHOa5iyWGzKdAlK3WqlHbP5oezR9OP?usp=sharing)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![Qiskit](https://img.shields.io/badge/Qiskit-0.44-purple.svg)](https://qiskit.org/)
[![arXiv](https://img.shields.io/badge/arXiv-2501.XXXXX-b31b1b.svg)](https://arxiv.org/)

**Official Implementation** of the research paper:  
> **"EBA-QR: An Entropy-Based Adaptive Quantum Image Representation Framework for Efficient Multi-Modal Image Processing"** > *Tanisha Debnath* > Institute of Engineering & Management, Kolkata, India.

---

## 📄 Abstract
Quantum Image Processing (QIP) promises exponential speedups but faces severe bottlenecks in data encoding efficiency on Noisy Intermediate-Scale Quantum (NISQ) devices. Most existing models (NEQR, FRQI) are content-agnostic, wasting computational resources on irrelevant background pixels.

This repository introduces **EBA-QR** (Entropy-Based Adaptive Quantum Representation), a content-aware encoding framework. By coupling **Local Shannon Entropy** with quantum circuit generation, EBA-QR dynamically allocates high-precision gates to Regions of Interest (ROI) while compressing background regions. 

**Key Results:**
* **78.1% Gate-Cost Reduction** on sparse SAR imagery (SSDD).
* **64.7% Reduction** on Brain Tumor MRI (Figshare) with high diagnostic safety.
* **O(1) Complexity** for background region encoding.
* **Validated on IBM Quantum** simulators and hardware.

<p align="center">
  <img src="EBA_QR_Flowchart_Final (1).png" alt="EBA-QR Architecture Flowchart" width="85%">
</p>

---

## 🚀 Quick Start (No Installation)
Reproduce all tables, ablation studies, and visualizations from the paper directly in your browser via Google Colab:

[**[Click Here to Open Notebook]**](https://colab.research.google.com/drive/1tUTHOa5iyWGzKdAlK3WqlHbP5oezR9OP?usp=sharing)

---

## ⚡ Key Features & Contributions

1.  **Entropy-Driven Encoding:** Implements a mathematical gate-cost optimization function $G(I)$ where background cost approaches zero.
2.  **Multi-Modal Benchmarking:** Tested on three heterogeneous datasets:
    * **SSDD:** Synthetic Aperture Radar (Sparse/Maritime).
    * **Brain Tumor MRI:** Medical Imaging (Safety-Critical).
    * **ICEYE:** Urban Satellite Imagery (Dense/Complex).
3.  **NISQ-Ready:** Circuits are optimized for depth and implemented using Qiskit.
4.  **Quantum Operations:** Supports Geometric Transformations (Flips) and Quantum Sobel Edge Detection.

---

## 🛠️ Installation

If you prefer running the code locally:

```bash
# 1. Clone the repository
git clone [https://github.com/YOUR_USERNAME/EBA-QR.git](https://github.com/YOUR_USERNAME/EBA-QR.git)
cd EBA-QR

# 2. Install dependencies
pip install -r requirements.txt

```

### 📂 Dataset Preparation

We utilize three publicly available datasets. Please download them from the links below and extract them into the `data/` directory:

1. **Brain Tumor MRI:** [Download from Figshare](https://figshare.com/articles/dataset/brain_tumor_dataset/1512427)
* *Description:* 3,064 T1-weighted MRI images containing meningioma, glioma, and pituitary tumors.
* *Destination:* `data/data_MRI/`


2. **SSDD (SAR Ship Detection):** [Download from Google Drive](https://drive.google.com/file/d/1glNJUGotrbEyk43twwB9556AdngJsynZ/view?usp=sharing)
* *Description:* Synthetic Aperture Radar imagery for maritime surveillance.
* *Destination:* `data/data_SSDD/`


3. **ICEYE SAR:** [Download from ICEYE](https://www.iceye.com/downloads/datasets)
* *Description:* High-resolution SAR imagery (e.g., Doha Airport).
* *Destination:* `data/data_ICEYE/`



**Directory Structure:**

```text
data/
├── data_SSDD/   # Contains .jpg/.xml files
├── data_MRI/    # Contains .mat files
└── data_ICEYE/  # Contains .png/.tif files

```

---

## 📊 Experimental Results

### 1. Gate Cost Comparison (vs. NEQR)

EBA-QR significantly reduces gate cost compared to the standard NEQR model, especially in sparse datasets.

| Dataset | Model | Avg. Gate Cost | Reduction |
| --- | --- | --- | --- |
| **SSDD (SAR)** | NEQR | 16,384 | - |
|  | **EBA-QR** | **10,900** | **33.5%** |
| **Brain MRI** | NEQR | 16,384 | - |
|  | **EBA-QR** | **12,006** | **26.7%** |

### 2. Disentangling Masking vs. Representation

We demonstrate that gains are due to the quantum architectural changes, not just classical masking.

| Dataset | NEQR+Mask (Baseline) | EBA-QR (Ours) | Structural Gain |
| --- | --- | --- | --- |
| **SSDD** | 16,074 | 15,803 | **1.7%** |
| **MRI** | 14,502 | 12,856 | **11.4%** |

---

## 💻 Usage

### Run Full Benchmarks

To generate the comparison tables against 10 baselines (FRQI, NEQR, GQIR, QLR, DCT-EFRQI, QPIE, TNR, INEQR, QRMW):

```bash
python main_benchmark.py

```

### Run Ablation Studies

To reproduce the Threshold Sensitivity () and Block Size () analysis (Section 7.3 of the paper):

```bash
python ablation_study.py

```

### Visualize Entropy & Operations

To generate Entropy Heatmaps and Quantum Sobel Edge Detection results:

```bash
python visualize_ops.py

```

---

## 📝 Citation

If you use this code or our results in your research, please cite the paper:

```bibtex
@article{debnath2025eba,
  title={EBA-QR: An Entropy-Based Adaptive Quantum Image Representation Framework for Efficient Multi-Modal Image Processing},
  author={Debnath, Tanisha},
  journal={arXiv preprint},
  year={2025},
  note={Under Review}
}

```

## 📜 License

This project is licensed under the **MIT License**.

## 📧 Contact

**Tanisha Debnath** Institute of Engineering & Management, Kolkata

Email: [tanishabdebnath@gmail.com](mailto:tanishabdebnath@gmail.com)

```

```
