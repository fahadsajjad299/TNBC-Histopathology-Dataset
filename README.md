# 🧬 TNBC-Multimodal-Dataset

**Structured Annotation and Metadata Integration of Triple-Negative 
Breast Cancer Histopathology Slides with Multimodal Imaging Links**

[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)
[![Patients: 114](https://img.shields.io/badge/Patients-114-blue)]()
[![Modalities: WSI+MRI](https://img.shields.io/badge/Modalities-WSI%2BMRI-green)]()
[![Status: In Progress](https://img.shields.io/badge/Status-In%20Progress-yellow)]()

---

## 📌 Overview

Triple-Negative Breast Cancer (TNBC) is one of the most aggressive 
subtypes of breast cancer, disproportionately affecting younger women 
in South Asian and Middle Eastern populations. Despite its clinical 
severity, no structured multimodal dataset exists that connects 
histopathology slides, MRI scans, AI-generated tumor annotations, 
and clinical outcomes in a single, FAIR-compliant, publicly accessible 
resource.

This dataset addresses that gap.

We curate a cohort of **114 confirmed TNBC patients** from the TCGA-BRCA 
collection, accessible through the NCI Imaging Data Commons (IDC). For 
each patient we provide:

- Structured clinical and demographic metadata
- Direct links to histopathology whole-slide images (WSI) and MRI scans
- AI-generated tumor region annotations (heatmaps, patch labels, masks)
- Survival and disease outcome data

All imaging data is accessed directly via IDC cloud infrastructure. 
No proprietary data is included.

---

## 👥 Team

| Name | Role |
|------|------|
| Fahad Sajjad | Team Leader, Bioinformatics Analyst |
| Jalal Ibrahim  | AI and ML Engineer |
---

## 📁 Repository Structure
```
TNBC-Histopathology-Dataset/
│
├── data/
│   ├── TNBC_Clinical_Data.csv       # Clinical metadata for 114 patients
│   ├── patient_ids.csv              # Confirmed TNBC patient ID list
│   └── imaging_links.csv            # IDC links to WSI and MRI per patient
│
├── annotations/
│   └── TCGA-XXXX/                   # One folder per patient (×114)
│       ├── heatmap_CLAM.png         # CLAM tumor heatmap
│       ├── heatmap_UNI.png          # UNI tumor heatmap
│       ├── patch_labels_CLAM.csv    # Patch-level tumor scores (CLAM)
│       ├── patch_labels_UNI.csv     # Patch-level tumor scores (UNI)
│       ├── tumor_mask_CLAM.npy      # Binary tumor mask (CLAM)
│       ├── tumor_mask_UNI.npy       # Binary tumor mask (UNI)
│       └── metadata.json            # Patient-level metadata
│
├── notebooks/
│   ├── 01_data_exploration.ipynb    # Clinical data analysis
│   ├── 02_slide_access.ipynb        # Accessing IDC slides via Colab
│   ├── 03_CLAM_annotation.ipynb     # CLAM model pipeline
│   └── 04_UNI_annotation.ipynb      # UNI model pipeline
│
├── scripts/
│   ├── filter_tnbc_patients.py      # Filter TNBC from TCGA-BRCA
│   ├── extract_metadata.py          # Build metadata JSON per patient
│   ├── run_clam.py                  # Run CLAM annotation pipeline
│   └── run_uni.py                   # Run UNI annotation pipeline
│
├── results/
│   ├── tumor_summary.csv            # Per-patient tumor statistics
│   ├── model_comparison.csv         # CLAM vs UNI comparison table
│   └── figures/                     # Summary plots and heatmap examples
│
├── docs/
│   ├── methodology.md               # Detailed methodology
│   └── data_dictionary.md           # Column descriptions for all CSVs
│
├── LICENSE                          # CC BY 4.0
└── README.md
```


---

## 🗂️ Dataset Description

### Cohort

| Property | Value |
|----------|-------|
| Cancer type | Triple-Negative Breast Cancer (TNBC) |
| Number of patients | 114 |
| Source collection | TCGA-BRCA via IDC |
| Slide type | FFPE H&E diagnostic slides (DX1) |
| Imaging modalities | Histopathology WSI + Breast MRI |
| Clinical data | Survival, stage, ER/PR status, demographics |

### Clinical Variables Available

| Variable | Completeness |
|----------|-------------|
| Patient ID | 114/114 |
| Diagnosis age | 114/114 |
| Overall survival (months) | 114/114 |
| Overall survival status | 114/114 |
| Disease free status | 103/114 |
| ER / PR status | 114/114 |
| Cancer stage | 111/114 |
| Race / ethnicity | 108/114 |

### Annotation Outputs Per Patient

| File | Description |
|------|-------------|
| heatmap_CLAM.png | Colored tumor probability map (CLAM model) |
| heatmap_UNI.png | Colored tumor probability map (UNI model) |
| patch_labels_CLAM.csv | Tile-level tumor scores from CLAM |
| patch_labels_UNI.csv | Tile-level tumor scores from UNI |
| tumor_mask_CLAM.npy | Binary pixel-level tumor mask (CLAM) |
| tumor_mask_UNI.npy | Binary pixel-level tumor mask (UNI) |
| metadata.json | Patient clinical info + imaging links |

---

## 🔬 Methodology

### Step 1 — Patient Selection
114 TNBC patients confirmed from TCGA-BRCA collection using 
ER-negative, PR-negative, HER2-negative receptor status from 
clinical metadata.

### Step 2 — Slide Access
All histopathology slides are accessed directly through IDC 
Google Cloud infrastructure using the idc-index Python package. 
Only DX1 (FFPE diagnostic) slides are used — frozen section 
slides (TSA, BSA) are excluded.

```python
from idc_index import IDCClient
client = IDCClient()

slides = client.sql_query("""
    SELECT i.PatientID, i.SeriesInstanceUID, s.ContainerIdentifier
    FROM index i
    JOIN sm_index s ON i.SeriesInstanceUID = s.SeriesInstanceUID
    WHERE i.collection_id = 'tcga_brca'
    AND i.Modality = 'SM'
    AND s.ContainerIdentifier LIKE '%-DX1'
""")
```

### Step 3 — AI Annotation Pipeline
Two AI models are run on each slide and results are compared:

**Model 1 — CLAM**
Clustering-Constrained Attention Multiple Instance Learning.
Widely used weakly supervised method for WSI tumor detection.

**Model 2 — UNI**
Universal Pathology Foundation Model (Chen et al., Nature 
Medicine 2024). Trained on 100,000+ pathology slides.

### Step 4 — Quality Control
- HistoQC used for slide quality filtering
- Stain normalization applied before inference
- Manual spot-check on 10% of patients
- Completeness verified for all metadata fields

### Step 5 — Release
All outputs organized per patient and released on Zenodo 
with a permanent DOI under CC BY 4.0 license.

---

## 🚀 How to Use This Dataset

### Load clinical data

```python
import pandas as pd

df = pd.read_csv('data/TNBC_Clinical_Data.csv')
print(f"Patients: {len(df)}")
print(df[['Patient ID', 'Overall Survival Status', 
          'Disease Free Status']].head())
```

### Load patient annotation

```python
import numpy as np
import json

patient = 'TCGA-A1-A0SK'

# Load metadata
with open(f'annotations/{patient}/metadata.json') as f:
    meta = json.load(f)

# Load tumor mask
mask = np.load(f'annotations/{patient}/tumor_mask_CLAM.npy')

# Load patch labels
import pandas as pd
patches = pd.read_csv(f'annotations/{patient}/patch_labels_CLAM.csv')

print(f"Tumor area: {mask.mean() * 100:.1f}%")
print(f"Total patches: {len(patches)}")
```

### Access slides directly from IDC (no download needed)

```python
from idc_index import IDCClient
client = IDCClient()

# All processing happens on Google Cloud
# No local storage required
```

---

## 📊 Key Statistics

*(To be updated upon completion of annotation pipeline)*

| Metric | Value |
|--------|-------|
| Total patients | 114 |
| Slides annotated | — |
| Average tumor area (CLAM) | — |
| Average tumor area (UNI) | — |
| CLAM vs UNI agreement | — |
| Deceased patients | 24/114 |
| Disease recurrence | 18/103 |

---

## ⚠️ Ethical Statement

All data is sourced from The Cancer Genome Atlas (TCGA) via the 
NCI Imaging Data Commons (IDC), which operates under dbGaP open 
access policy. All patient data is fully de-identified. No new 
patient data was collected for this study. No additional ethical 
approval is required beyond compliance with TCGA data use policy.

Users of this dataset must:
- Not attempt to re-identify any patient
- Report any suspected data breaches
- Use data in accordance with applicable research ethics standards
- Cite this dataset appropriately in any publication

---

## 📥 Data Access

| Resource | Link |
|----------|------|
| Histopathology slides | IDC Viewer |
| MRI scans | IDC Viewer |
| Zenodo release | Coming soon |
| DOI | Coming soon |

Primary data remains hosted on IDC and TCIA. This repository 
contains derived annotations and structured metadata only.

---

## 📖 Citation

If you use this dataset please cite:
