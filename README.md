
# EBA-QR: Entropy-Based Adaptive Quantum Image Representation

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1tUTHOa5iyWGzKdAlK3WqlHbP5oezR9OP?usp=sharing)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![Qiskit](https://img.shields.io/badge/Qiskit-0.44-purple.svg)](https://qiskit.org/)
[![arXiv](https://img.shields.io/badge/arXiv-2501.XXXXX-b31b1b.svg)](https://arxiv.org/)

**Official implementation** of:

> **“EBA-QR: An Entropy-Based Adaptive Quantum Image Representation Framework for Efficient Multi-Modal Image Processing”**  
> *Tanisha Debnath* – Institute of Engineering & Management, Kolkata, India.

---

## Overview

Quantum Image Processing (QIP) promises powerful speedups but is heavily constrained by the cost of encoding classical images into quantum states on NISQ devices. Classical QIR models such as FRQI and NEQR are **content-agnostic**, spending the same gate budget on background and Regions of Interest (ROIs), which is wasteful for sparse scenes like SAR ship detection or tumor-focused MRI scans.

**EBA-QR** (Entropy-Based Adaptive Quantum Representation) is a **content-aware** quantum image representation that:

- Computes **local Shannon entropy** over image blocks.
- Uses a tunable threshold to classify blocks as ROI vs. background.
- Allocates **high-precision NEQR-style gates** only to high-entropy blocks.
- Applies **gate-skipping** to background blocks so their cost approaches **O(1)** per block.

In experiments on SSDD SAR, Brain Tumor MRI and ICEYE Doha Airport SAR, EBA-QR reduces gate cost while preserving NEQR-level fidelity in ROIs and maintaining task-relevant structure (e.g., tumors, ships, runways).

---

## One-Click Reproducibility (Colab)

All experiments, figures and tables from the paper are implemented in a **single Google Colab notebook**:

👉 **[Open the EBA-QR Colab Notebook](https://colab.research.google.com/drive/1tUTHOa5iyWGzKdAlK3WqlHbP5oezR9OP?usp=sharing)**

The notebook is designed to be run **entirely in Colab** (no local Jupyter setup required) and includes:

- Dataset preparation and loading for:
  - **Brain Tumor MRI** (Figshare).
  - **SSDD** SAR ship detection.
  - **ICEYE** SAR (e.g., Doha Airport).
- EBA-QR circuit construction and the **NEQR+Mask** control baseline.
- Gate-cost, depth and ROI-fidelity evaluation across all 11 QIR models.
- Ablation studies:
  - Entropy threshold \( \tau \).
  - Block size \( b \) for local entropy.
- Visualization of:
  - Entropy maps and ROI masks.
  - Quantum geometric transforms (horizontal/vertical flips).
  - Sobel-based edge maps for MRI, SSDD and ICEYE.
- Small-scale IBM Quantum validation:
  - \(2\times 2\) and \(4\times 4\) EBA-QR circuits.
  - Gate counts, measurement histograms and Q-sphere plots.

Run the cells from top to bottom to reproduce the results in the paper.

---

## Datasets

The notebook expects you to download three **public** datasets. This repository **does not** redistribute raw data; please download from the official sources below and follow the folder structure.

### 1. Brain Tumor MRI (Figshare)

- **Source:** Figshare – “brain tumor dataset” (3,064 T1-weighted CE MRI images with meningioma, glioma and pituitary tumors).
- **Download:**  
  [https://figshare.com/articles/dataset/brain_tumor_dataset/1512427](https://figshare.com/articles/dataset/brain_tumor_dataset/1512427)
- **Destination folder:**  
  `data/data_MRI/`

### 2. SSDD – SAR Ship Detection

- **Source:** SAR Ship Detection Dataset (SSDD), SAR imagery for maritime ship detection.
- **Download:**  
  [https://drive.google.com/file/d/1glNJUGotrbEyk43twwB9556AdngJsynZ/view?usp=sharing](https://drive.google.com/file/d/1glNJUGotrbEyk43twwB9556AdngJsynZ/view?usp=sharing)
- **Destination folder:**  
  `data/data_SSDD/`

### 3. ICEYE SAR

- **Source:** ICEYE open SAR datasets (e.g., Doha Airport imagery).
- **Download:**  
  [https://www.iceye.com/downloads/datasets](https://www.iceye.com/downloads/datasets)
- **Destination folder:**  
  `data/data_ICEYE/`

**Directory Structure (after download):**

```text
data/
├── data_SSDD/   # SSDD SAR ship detection images
├── data_MRI/    # Brain tumor MRI images
└── data_ICEYE/  # ICEYE SAR crops (e.g., Doha Airport)
```

Once the files are placed correctly, the Colab notebook handles resizing to \(32\times 32\), grayscale conversion and entropy-based block partitioning.

---

## Key Ideas and Contributions

- **Entropy-driven gate allocation**  
  Local Shannon entropy guides where to spend or save gates, shifting the focus from “qubits per pixel” to “gates per unit of useful structure”.

- **Formal gate-cost model**  
  A gate-cost function \( G(I) \) shows that background cost can be driven towards \( G_{\text{low}} \approx 0 \), while ROI cost scales with the number of high-entropy blocks \(N_{\text{roi}}\) instead of the full image size \(N^2\).

- **NEQR+Mask baseline**  
  A control model that uses the **same entropy-based ROI mask** as EBA-QR but encodes the masked image with the **standard NEQR circuit**, separating the benefit of adaptive gate allocation from classical masking.

- **NISQ-ready circuits**  
  Implemented in **Qiskit**, evaluated on Aer simulators and small IBM Quantum backends (via `qasm_simulator` and real devices), focusing on realistic gate counts, depth and noise.

- **Processing-ready QIR**  
  EBA-QR states support:
  - Quantum **geometric transformations** (horizontal and vertical flips).
  - Quantum-compatible **Sobel edge detection** (via hybrid quantum-classical routines).
  making EBA-QR suitable as a front-end for QCNNs and other QML models.

---

## Main Results (From the Paper)

All numbers below are for \(32\times 32\) images, averaged over SSDD, Brain Tumor MRI and ICEYE subsets, and are recomputed in the Colab.

### Gate Cost vs. NEQR and Others

- **SSDD (sparse SAR)**  
  - NEQR: ~16,384 gates per image.  
  - **EBA-QR**: ~10,900 gates  
    → **≈33.5% gate-cost reduction** vs. NEQR on SSDD, while maintaining ~99% reconstruction fidelity in ship ROIs.  
  - Strongest savings on SSDD due to large low-entropy ocean background.

- **Brain Tumor MRI (safety-critical)**  
  - NEQR: ~16,384 gates.  
  - **EBA-QR**: ~12,006 gates  
    → **≈26.7% improvement** in gate cost, **100% fidelity** on ROIs (tumor + surrounding tissue) and maximum safety score in the evaluation protocol.  
  - EBA-QR avoids lossy artifacts seen in DCT-EFRQI and QLR, which can blur tumor edges.

- **ICEYE Doha Airport (dense urban SAR)**  
  - NEQR: ~16,384 gates.  
  - **EBA-QR**: ~11,825 gates  
    → **≈27.8% gate-cost reduction** while keeping ~99% fidelity and high robustness to cluttered, high-entropy infrastructure scenes.

### Beyond Classical Masking (NEQR+Mask)

To separate masking from representation design:

- **NEQR+Mask** uses the same entropy-based ROI mask but keeps the NEQR circuit.
- On SSDD:
  - NEQR: 16,384 gates  
  - NEQR+Mask: 16,074 gates  
  - **EBA-QR**: 15,803 gates  
  → ≈1.7% additional structural gain due to adaptive gate allocation.
- On MRI:
  - NEQR: 16,384 gates  
  - NEQR+Mask: 14,502 gates  
  - **EBA-QR**: 12,856 gates  
  → **≈11.4% additional structural gain**, showing that most savings on MRI come from the EBA-QR representation, not just masking.

### Ablations and IBM Quantum Validation

- **Entropy threshold \( \tau \)**  
  - Increasing \( \tau \) reduces gate cost, with a safe operating band \(\tau \in [0.3, 0.7]\) that preserves NEQR-level fidelity within 1–2% on SSDD and MRI.
- **Block size \( b \)**  
  - Small \(b\) (e.g., 2) → lowest gate cost but high classical overhead.  
  - Large \(b\) (≥16) → NEQR-like gate cost, low overhead.  
  - \(b = 4\) provides a good trade-off and is used in the main experiments.
- **IBM Quantum**  
  - \(2\times 2\) EBA-QR circuits run on real devices match simulator histograms and Q-sphere amplitudes, confirming that entropy-guided gate skipping preserves NEQR-style semantics under realistic NISQ noise.

---

## Local Use (Optional)

The recommended way to run the code is via **Google Colab** (link above). If you still want to clone the repository locally:

```bash
git clone https://github.com/TanishaDebnath/EBA-QR.git
cd EBA-QR

# (Optional) create a virtual environment
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate

pip install -r requirements.txt
```

Then you can:

- Open the Colab notebook link in your browser (no Jupyter install needed), or
- Download the notebook from Colab and open it in your local Jupyter / VS Code if you prefer.

---

## Citation

If you use this repository or build upon EBA-QR, please cite:

```bibtex
@article{debnath2025eba,
  title   = {EBA-QR: An Entropy-Based Adaptive Quantum Image Representation Framework for Efficient Multi-Modal Image Processing},
  author  = {Debnath, Tanisha},
  journal = {arXiv preprint},
  year    = {2025},
  note    = {Under review}
}
```

---

## License

This project is licensed under the **MIT License**. See `LICENSE` for details.

---

## Contact

For questions, feedback or collaborations:

- **Author:** Tanisha Debnath  
- **Affiliation:** Institute of Engineering & Management, Kolkata  
- **Email:** [tanishabdebnath@gmail.com](mailto:tanishabdebnath@gmail.com)  
- **Project:** [https://github.com/TanishaDebnath/EBA-QR](https://github.com/TanishaDebnath/EBA-QR)
```

