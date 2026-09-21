<div align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/b/bd/Indian_Space_Research_Organisation_Logo.svg" alt="ISRO Logo" width="120" />

  <h1>🌌 Lunar Image Registration Pipeline</h1>
  <p><strong>Chandrayaan-2 Optical Image Correspondence & Geometric Alignment</strong></p>

  <p>
    <img src="https://img.shields.io/badge/SIH-2026-orange?style=for-the-badge&logo=hackerearth" alt="SIH 2026" />
    <img src="https://img.shields.io/badge/Problem-26166-blue?style=for-the-badge" alt="PS 26166" />
    <img src="https://img.shields.io/badge/Python-3.10%2B-green?style=for-the-badge&logo=python" alt="Python 3.10+" />
    <img src="https://img.shields.io/badge/License-MIT-lightgrey?style=for-the-badge" alt="License" />
  </p>
  
  <i>Developed for the Smart India Hackathon 2026 • Department of Space / ISRO</i>
</div>

---

## 📑 Table of Contents

1. [Overview](#-overview)
2. [Problem Statement](#-problem-statement)
3. [Pipeline Architecture](#-pipeline-architecture)
4. [Key Features](#-key-features)
5. [File Structure](#-file-structure)
6. [Installation & Setup](#-installation--setup)
7. [Usage Guide](#-usage-guide)
8. [Output & Metrics](#-output--metrics)
9. [Demo Data](#-demo-data)
10. [Real Data Integration](#-real-data-integration)
11. [Development Roadmap](#-development-roadmap)

---

## 🚀 Overview

A robust baseline pipeline for registering Lunar Source Payload Imagery (Chandrayaan-2 TMC-2, OHRC, IIRS) against Global Reference Base Maps using **SIFT feature matching** and **RANSAC-based homography estimation**.

Given two lunar images of the same region captured at different times, sun angles, or viewpoint offsets, this system automatically aligns them with geometric precision, handling variations in illumination and scale. The pipeline outputs matched visualizations, registered overlays, and quantitative quality metrics validated against ISRO requirements.

---

## 🎯 Problem Statement

* **Organization:** Indian Space Research Organisation (ISRO)
* **Theme:** Space Technology
* **Category:** Software
* **Problem ID:** SIH26166
* **Objective:** Develop a generic software solution for finding correspondence between Chandrayaan-2 acquired optical images and Lunar reference images, enabling automated multi-temporal image alignment for lunar mapping and change detection.

---

## 🔬 Pipeline Architecture

The registration engine executes the following sequential processing stages:

### Stage 1: Image Loading & Preprocessing 📥
- **Format Support:** PDS4 `.IMG`/`.XML` (GDAL-based) or standard PNG/JPG
- **Illumination Normalization:** CLAHE (Contrast Limited Adaptive Histogram Equalization) to handle varying sun angles and shadows
- **Noise Reduction:** Gaussian smoothing for stable feature detection

### Stage 2: Feature Detection 🔍
- **Detector:** SIFT (Scale-Invariant Feature Transform)
- **Keypoint Extraction:** Automatic detection of distinctive lunar terrain features (crater rims, ridge intersections, topological landmarks)
- **Scale & Rotation Invariance:** Handles images with different resolutions and orientations

### Stage 3: Feature Matching 🔗
- **Matcher:** FLANN (Fast Approximate Nearest Neighbors)
- **Filtering:** Lowe's ratio test (0.7 threshold) to eliminate ambiguous matches
- **Output:** Validated correspondence pairs between source and reference images

### Stage 4: Geometric Estimation 📐
- **Algorithm:** RANSAC (Random Sample Consensus)
- **Transformation Model:** Homography matrix (planar projection)
- **Robustness:** Automatically filters outliers and inconsistent matches
- **Output:** Geometrically consistent inlier set

### Stage 5: Image Alignment & Warp 🎯
- **Transformation:** Applies estimated homography to warp source image onto reference coordinate system
- **Interpolation:** Bilinear resampling for smooth geometric alignment
- **Output:** Registered image in reference frame with pixel-level precision

### Stage 6: Metrics & Verification 📊
- Quantitative evaluation of alignment quality
- Real-time feedback for validation

---

## ✨ Key Features

| Feature | Capability |
|---------|-----------|
| **Multi-format Input** | PDS4 (GDAL), PNG, JPG support |
| **Illumination Robustness** | CLAHE preprocessing for sun-angle variations |
| **Scale Handling** | SIFT-based automatic scale detection |
| **Rotation Invariance** | Handles arbitrary image orientations |
| **Demo Data Generator** | Synthetic crater-pair generation for testing |
| **Comprehensive Metrics** | RMSE, inlier ratio, keypoint counts, runtime analysis |
| **Visualization Output** | Match overlays, before/after panels, metric reports |

---

## 📦 File Structure

```
sih2026-lunar-registration/
├── 📜 register.py              # Core registration pipeline logic
├── 📜 make_demo_pair.py        # Synthetic dataset generator
├── 📜 requirements.txt         # Python dependency matrix
└── 📂 demo_data/               # Generated test datasets
    ├── source.png              # Source lunar image
    └── target.png              # Target reference image
```

---

## 💻 Installation & Setup

**Prerequisites:** Python 3.10 or higher

### Option 1: Standard Setup

```bash
git clone https://github.com/ssjb-kakarot/sih2026-lunar-registration
cd sih2026-lunar-registration

# Create virtual environment
python3 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\Activate.ps1

# Install dependencies
pip install -r requirements.txt
```

### Option 2: Windows PowerShell

```powershell
git clone https://github.com/ssjb-kakarot/sih2026-lunar-registration
cd sih2026-lunar-registration

# Create Python 3.10 Virtual Environment
py -3.10 -m venv venv

# Activate
.\venv\Scripts\Activate.ps1

# Install dependencies
pip install opencv-python numpy gdal
```

### Core Dependencies

```
opencv-python >= 4.5.0
numpy >= 1.21.0
gdal >= 3.4.0  # Only for PDS4 .IMG/.XML files
```

---

## 🎮 Usage Guide

### Quick Start: Synthetic Demo Data

Generate a synthetic crater-pair and run the full pipeline:

```bash
# Step 1: Generate demo data
python3 make_demo_pair.py --out demo_data

# Step 2: Run registration
python3 register.py demo_data/source.png demo_data/target.png --out demo_output
```

**Output files created:**
- `demo_output/matches.png` — Keypoint correspondence visualization
- `demo_output/before_after.png` — 3-panel alignment comparison
- `demo_output/metrics.csv` — Quantitative evaluation metrics

### Real Chandrayaan-2 Data

```python
from register import load_pds_image, run_pipeline

# Register using PDS4 format
run_pipeline(
    "ch2_tmc_ncn_20231026T0943001971_d_img_d18.xml",
    "ch2_tmc_ncn_20231222T0751399116_d_img_d18.xml",
    "output_real",
    loader=load_pds_image,
)
```

### Python API

```python
from register import run_pipeline

run_pipeline(
    source_image="source.png",
    target_image="target.png", 
    output_dir="output",
    min_matches=10,  # Minimum feature correspondences required
)
```

---

## 📊 Output & Metrics

Each pipeline execution generates comprehensive output:

### Visual Outputs

1. **Keypoint Matches** (`matches.png`)
   - Detected features in both images
   - Feature correspondence lines
   - Color-coded for visibility

2. **Before/After Overlay** (`before_after.png`)
   - Source image (original)
   - Target reference image
   - Registered source overlaid on target

### Quantitative Metrics (`metrics.csv`)

| Metric | Description | Target |
|--------|-------------|--------|
| **Keypoints (Source)** | SIFT features detected in source image | High |
| **Keypoints (Target)** | SIFT features detected in target image | High |
| **Good Matches** | Matches passing Lowe's ratio test | High |
| **Inliers** | Matches surviving RANSAC geometric filtering | High |
| **Inlier Ratio** | % of matches that are geometrically consistent | >80% |
| **RMSE (pixels)** | Root mean square alignment error | <1.0 px |
| **Runtime (seconds)** | Total pipeline execution time | <2.0 s |

---

## 🧪 Demo Data Generation

The `make_demo_pair.py` script generates synthetic lunar crater imagery for testing without downloading large satellite datasets.

### What It Creates

The generator produces a realistic crater-like terrain pair by:

1. **Base Terrain:** Random noise texture simulating lunar surface irregularities
2. **Crater Placement:** ~35 synthetically rendered craters with:
   - Dark crater floor
   - Bright rim highlights
   - Shaded inner walls
3. **Target Transformation:** Creates the second image by applying:
   - Rotation (~7°) — simulating orbital trajectory changes
   - Scale change (~0.93x) — simulating altitude variations
   - Darkening (~20%) — mimicking sun angle shifts

### Usage

```bash
python3 make_demo_pair.py --out demo_data
```

**Output:**
- `demo_data/source.png` — Original synthetic crater pair
- `demo_data/target.png` — Transformed version (rotated, scaled, darkened)

### Important Note

⚠️ **Demo data is for development and testing only.** Metrics reported on synthetic data do not reflect real Chandrayaan-2 performance. Download and process actual orbital imagery via `load_pds_image` for production results.

---

## 🌍 Real Data Integration

### Chandrayaan-2 Data Sources

| Dataset | Satellite / Sensor | Access Portal |
|---------|-------------------|---|
| **Source Payload** | Chandrayaan-2 (TMC-2, OHRC, IIRS) | [ISSDC Map Browse](https://chmapbrowse.issdc.gov.in/) |
| **Reference Maps** | LRO NAC (Lunar Reconnaissance Orbiter) | [LROC QuickMap](https://quickmap.lroc.im-ldi.com) |
| **Reference Maps** | SELENE (Kaguya) | [LROC Data Archive](https://lroc.im-ldi.com/images/downloads/) |

### Current Status

✅ **Pipeline:** Validated end-to-end on synthetic demo data  
🔄 **Real Chandrayaan-2 Data:** Download in progress from ISSDC (Tycho Crater region)  
⏳ **Production Results:** Metrics from real orbital imagery coming soon

### Benchmark (Synthetic Data)

| Metric | Result |
|--------|--------|
| Keypoints (Source / Target) | 284 / 93 |
| Good Matches | 30 |
| Inliers | 28 |
| Inlier Ratio | 93.3% |
| RMSE | 0.98 pixels |
| Runtime | 0.52 seconds |

---

## 🛣️ Development Roadmap

The pipeline follows a structured progression from baseline to production:

```
Phase 1: Fundamentals
  └─ SIFT baseline → RANSAC geometric registration

Phase 2: Robustness
  └─ Illumination handling → Scale/viewpoint invariance

Phase 3: Advanced Algorithms
  └─ Affine-SIFT (ASIFT) → Thin Plate Splines → Sub-pixel refinement

Phase 4: Extended Capabilities
  └─ Uniform correspondence distribution → Cross-sensor registration

Phase 5: Production
  └─ GPU acceleration → Batch processing → Real Chandrayaan-2 validation
```

### Planned Enhancements

- **Multi-threaded ASIFT** for extreme viewpoint variations
- **Multi-Scale Retinex (MSR)** for robust illumination normalization
- **Sub-pixel corner refinement** via gradient analysis
- **Thin Plate Splines (TPS)** for non-rigid crater deformation
- **GPU acceleration** using CuPy and OpenCV-CUDA
- **Batch processing CLI** for folder-to-folder registration
- **Multi-spectral support** for thermal/hyperspectral alignment

---

<div align="center">
  <b>Built with precision and passion for the Indian Space Research Organisation.</b><br>
  <i>Smart India Hackathon 2026</i>
</div>
