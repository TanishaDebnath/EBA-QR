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

Quantum Image Processing (QIP) promises speedups for large-scale visual workloads but is heavily constrained by the cost of encoding classical images into quantum states on NISQ devices. Classical QIR models such as FRQI and NEQR are **content-agnostic**, spending the same gate budget on background and Regions of Interest (ROIs), which is wasteful for sparse scenes like SAR ship detection or tumor-focused MRI.

**EBA-QR** (Entropy-Based Adaptive Quantum Representation) is a **content-aware** quantum image representation that:

- Computes **local Shannon entropy** over image blocks.
- Uses a tunable threshold to classify blocks as ROI vs. background.
- Allocates **high-precision NEQR-style gates** only to high-entropy blocks.
- Applies **gate-skipping** to background blocks so their cost approaches **O(1)** per block.

In experiments on SSDD SAR, Brain Tumor MRI and ICEYE Doha Airport SAR, EBA-QR reduces gate cost while preserving NEQR-level fidelity in ROIs and maintaining task-relevant structure (tumors, ships, runways).

---

## One-Click Reproducibility (Colab)

All experiments, figures and tables from the paper are implemented in a **single Google Colab notebook**:

👉 **[Open the EBA-QR Colab Notebook](https://colab.research.google.com/drive/1tUTHOa5iyWGzKdAlK3WqlHbP5oezR9OP?usp=sharing)**

The notebook is meant to be run **entirely in Colab** (no local Jupyter setup required) and includes:

- Dataset preparation and loading for:
  - **Brain Tumor MRI** (Figshare).
  - **SSDD** SAR ship detection.
  - **ICEYE** SAR (e.g., Doha Airport).
- EBA-QR circuit construction and the **NEQR+Mask** control baseline.
- Gate-cost, depth and ROI-fidelity evaluation across **11** QIR models.
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

This repository **does not** redistribute raw data. Please download the following **public** datasets and place them as shown.

### 1. Brain Tumor MRI (Figshare)

- **Source:** Figshare – “brain tumor dataset” (3,064 T1-weighted CE MRI images with meningioma, glioma and pituitary tumors)  
- **Download:**  
  https://figshare.com/articles/dataset/brain_tumor_dataset/1512427  
- **Destination folder:**  
  `data/data_MRI/`

### 2. SSDD – SAR Ship Detection

- **Source:** SAR Ship Detection Dataset (SSDD)  
- **Download:**  
  https://drive.google.com/file/d/1glNJUGotrbEyk43twwB9556AdngJsynZ/view?usp=sharing  
- **Destination folder:**  
  `data/data_SSDD/`

### 3. ICEYE SAR

- **Source:** ICEYE open SAR datasets (e.g., Doha Airport imagery)  
- **Download:**  
  https://www.iceye.com/downloads/datasets  
- **Destination folder:**  
  `data/data_ICEYE/`

**Directory structure:**

```text
data/
├── data_SSDD/   # SSDD SAR ship detection images
├── data_MRI/    # Brain tumor MRI images
└── data_ICEYE/  # ICEYE SAR crops (e.g., Doha Airport)


The Colab notebook handles resizing to 
32
×
32
32×32, grayscale conversion and entropy-based block partitioning once files are in place.

Key Ideas and Contributions
Entropy-driven gate allocation
Local Shannon entropy guides where to spend or save gates, shifting the focus from “qubits per pixel” to “gates per unit of useful structure.”

Formal gate-cost model
EBA-QR defines a gate-cost function

G
EBA
(
I
)
=
∑
k
=
1
N
blocks
[
I
(
H
(
R
k
)
>
τ
)
 
G
high
+
I
(
H
(
R
k
)
≤
τ
)
 
G
low
]
,
G 
EBA
 (I)= 
k=1
∑
N 
blocks
 
 [I(H(R 
k
 )>τ)G 
high
 +I(H(R 
k
 )≤τ)G 
low
 ],
where each block 
R
k
R 
k
  is classified as:

ROI if 
H
(
R
k
)
>
τ
H(R 
k
 )>τ → full budget 
G
high
G 
high
 .

Background if 
H
(
R
k
)
≤
τ
H(R 
k
 )≤τ → minimal budget 
G
low
≈
0
G 
low
 ≈0.

As a result, the total cost scales with the number of ROI blocks 
N
roi
N 
roi
 :

G
EBA
(
I
)
≈
Θ
(
N
roi
)
,
G 
EBA
 (I)≈Θ(N 
roi
 ),
instead of 
O
(
N
2
)
O(N 
2
 ) as in NEQR. For sparse scenes, this yields large gate savings while keeping NEQR-level fidelity where it matters.

NEQR+Mask baseline
A control model that uses the same entropy-based ROI mask as EBA-QR but encodes the masked image with the standard NEQR circuit, isolating the effect of adaptive gate allocation from classical masking.

NISQ-ready circuits
Implemented in Qiskit, evaluated on Aer simulators and IBM Quantum backends, with realistic gate counts, depths and noise.

Processing-ready QIR
EBA-QR states support:

Quantum geometric transformations (horizontal / vertical flips).

Quantum-compatible Sobel edge detection (hybrid quantum–classical).
making EBA-QR suitable as a front-end for QCNNs and other QML models.

Main Results (Paper Summary)
All numbers are for 
32
×
32
32×32 images, averaged over SSDD, Brain Tumor MRI and ICEYE subsets, and recomputed in the Colab notebook.

Gate Cost vs. NEQR
SSDD (sparse SAR)

NEQR: ~16,384 gates

EBA-QR: ~10,900 gates
→ ≈33.5% fewer gates, with ~99% ROI fidelity on ship targets.

Brain Tumor MRI (safety‑critical)

NEQR: ~16,384 gates

EBA-QR: ~12,006 gates
→ ≈26.7% reduction, 100% ROI fidelity on tumor + surrounding tissue.

EBA-QR avoids the lossy blur seen in transform-based schemes (e.g., DCT‑EFRQI, QLR) at this resolution.

ICEYE Doha Airport (dense urban SAR)

NEQR: ~16,384 gates

EBA-QR: ~11,825 gates
→ ≈27.8% reduction, with ~99% fidelity for complex runways, terminals and aircraft.

Beyond Classical Masking (NEQR+Mask)
Same entropy mask for NEQR+Mask and EBA-QR.

SSDD:

NEQR: 16,384

NEQR+Mask: 16,074

EBA-QR: 15,803
→ ≈1.7% extra structural gain from adaptive gate allocation.

MRI:

NEQR: 16,384

NEQR+Mask: 14,502

EBA-QR: 12,856
→ ≈11.4% extra gain, showing that most MRI savings come from the EBA-QR representation, not just masking.

Ablations and IBM Quantum Validation
Entropy threshold 
τ
τ:
τ
∈
[
0.3
,
0.7
]
τ∈[0.3,0.7] provides consistent gate savings with fidelity within 1–2% of NEQR on SSDD and MRI.

Block size 
b
b:
b
=
4
b=4 gives a good trade-off between lower gate cost and moderate classical entropy time, and is used in main experiments.

IBM Quantum:
2
×
2
2×2 / 
4
×
4
4×4 EBA-QR circuits on real hardware match simulator histograms and Q-spheres, confirming that entropy-guided gate skipping preserves NEQR-style semantics under NISQ noise.

Local Use (Optional)
The recommended path is via Google Colab (link above). If you still want a local clone:

bash
git clone https://github.com/TanishaDebnath/EBA-QR.git
cd EBA-QR

# (Optional) create a virtual environment
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate

pip install -r requirements.txt
Then either:

Use the Colab link in your browser, or

Download the notebook from Colab and open it locally in Jupyter / VS Code.

Citation
If you use this repository or build upon EBA-QR, please cite:

text
@article{debnath2025eba,
  title   = {EBA-QR: An Entropy-Based Adaptive Quantum Image Representation Framework for Efficient Multi-Modal Image Processing},
  author  = {Debnath, Tanisha},
  journal = {arXiv preprint},
  year    = {2025},
  note    = {Under review}
}
License
This project is licensed under the MIT License. See LICENSE for details.

Contact
For questions, feedback or collaborations:

Author: Tanisha Debnath

Affiliation: Institute of Engineering & Management, Kolkata

Email: tanishabdebnath@gmail.com

Project: https://github.com/TanishaDebnath/EBA-QR
