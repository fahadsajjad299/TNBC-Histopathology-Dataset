# 🧬 TNBC Histopathology Dataset

> **Structured Annotation and Metadata Integration of Triple-Negative Breast Cancer
> Histopathology Slides with Treatment Response and Multimodal Imaging Links**

![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)
![Patients](https://img.shields.io/badge/Patients-114-blue)
![Status](https://img.shields.io/badge/Status-In%20Progress-orange)

---

## 📌 Project Overview

This project curates and enhances publicly available histopathology whole-slide images
for a cohort of **114 Triple-Negative Breast Cancer (TNBC)** patients. It integrates:

- Chemotherapy treatment response metadata
- Structured clinical and imaging metadata
- AI-based tumor region annotations
- Cross-referenced MRI and pathology slide links

Data is sourced from the [Imaging Data Commons (IDC)](https://imaging.datacommons.cancer.gov/)
and related TCIA collections.

---

## 👥 Team

| Name | Role |
|------|------|
| Fahad Sajjad | Team Leader, Bioinformatics Analyst |


---

## 📁 Repository Structure

TNBC-Histopathology-Dataset/
├── data/                  # Patient cohort CSV, metadata, imaging links
├── annotations/           # Tumor masks and patch-level labels
├── scripts/               # Python scripts for curation and annotation
├── notebooks/             # Exploratory analysis notebooks
├── docs/                  # Research proposal and methodology
└── results/               # Summary statistics and outputs

---

## 🎯 Objectives

1. Finalize curation of the 114-patient TNBC cohort
2. Integrate chemotherapy treatment and response metadata
3. Provide organized links to pathology slides and MRI series
4. Generate automated tumor region annotations
5. Produce structured patch-level labels and derived outputs
6. Release a comprehensive derived dataset with documentation

---

## 🗂️ Data Description

Each patient record includes:

- ✅ Whole-slide histopathology images (WSI)
- ✅ Corresponding breast MRI scans
- ✅ Chemotherapy treatment details
- ✅ Treatment response indicators (pCR / non-pCR)
- ✅ Structured clinical and imaging metadata
- ✅ Direct viewer links to pathology slides and MRI series

---

## 🔬 Methodology

### Data Curation
- Confirm TNBC subtype status for all patients
- Extract and standardize chemotherapy and response data
- Map and document pathology and MRI links

### Image Processing
- Tile WSIs into standardized patches
- Apply stain normalization and artifact filtering
- Generate tumor probability maps using AI-based detection models

### Quality Control
- Validate annotations with quantitative metrics
- Verify metadata completeness and link consistency
- Selective manual review

---

## ⏱️ Timeline

| Month | Milestone |
|-------|-----------|
| Month 1 | Data preparation, tech setup, AI system configuration |
| Month 2 | AI tumor mapping, QC, documentation, public release |

---

## 🔗 Data Access

Histopathology slides are publicly accessible via:
- [Imaging Data Commons (IDC)](https://imaging.datacommons.cancer.gov/)
- [TCIA Collections](https://www.cancerimagingarchive.net/)

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).

---

## 🙏 Acknowledgements

Data sourced from TCGA Breast Cancer collections available through the
Imaging Data Commons (IDC) and The Cancer Imaging Archive (TCIA).
