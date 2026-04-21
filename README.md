# 🧬 TNBC-Histopathology-Dataset
**Structured Annotation and Metadata Integration of Triple-Negative Breast Cancer Histopathology Slides**

![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)
![Patients: 153](https://img.shields.io/badge/Patients-153-blue)
![Modality: WSI](https://img.shields.io/badge/Modality-WSI-green)
![Status: In Progress](https://img.shields.io/badge/Status-In%20Progress-yellow)

---

## 📌 Overview

Triple-Negative Breast Cancer (TNBC) is one of the most aggressive subtypes of breast cancer, disproportionately affecting younger women in South Asian and Middle Eastern populations. Despite its clinical severity, no structured dataset exists that connects histopathology slides, AI-generated tumor annotations, and clinical outcomes in a single, FAIR-compliant, publicly accessible resource.

This dataset addresses that gap.

We curate a cohort of **153 confirmed TNBC patients** from the TCGA-BRCA collection, sourced from [cBioPortal](https://www.cbioportal.org/) and accessible through the [NCI Genomic Data Commons (GDC)](https://portal.gdc.cancer.gov/). For each patient we provide:

- Structured clinical and demographic metadata
- Direct links to histopathology whole-slide images (WSI) via GDC
- AI-generated tumor region annotations (heatmaps, patch labels, masks)
- Survival and disease outcome data

All imaging data is accessed directly via GDC cloud infrastructure. No proprietary data is included.

---

## 👥 Team

| Name | Role |
|---|---|
| Fahad Sajjad | Team Leader, Bioinformatics Analyst |
| Jalal Ibrahim | AI and ML Engineer |

---

## 📁 Repository Structure

```
TNBC-Histopathology-Dataset/
│
├── data/
│   ├── TNBC_confirmed_patients.csv      # Full clinical data — 153 confirmed TNBC patients
│   ├── TNBC_master.csv                  # Curated master table for AI training
│   ├── TNBC_patient_slide_ids.csv       # Patient IDs + GDC Pathology UUIDs for slide access
│   ├── TNBC_exclusion_log.csv           # Audit log — why each patient was excluded
│   └── brca_tcga_clinical_data.tsv      # Raw cBioPortal download (original input)
│
├── annotations/
│   └── TCGA-XXXX/                       # One folder per patient (×153)
│       ├── heatmap_CLAM.png             # CLAM tumor heatmap
│       ├── heatmap_UNI.png              # UNI tumor heatmap
│       ├── patch_labels_CLAM.csv        # Patch-level tumor scores (CLAM)
│       ├── patch_labels_UNI.csv         # Patch-level tumor scores (UNI)
│       ├── tumor_mask_CLAM.npy          # Binary tumor mask (CLAM)
│       ├── tumor_mask_UNI.npy           # Binary tumor mask (UNI)
│       └── metadata.json                # Patient-level metadata + imaging links
│
├── notebooks/
│   ├── 01_data_exploration.ipynb        # Clinical data analysis
│   ├── 02_slide_access.ipynb            # Accessing GDC slides via Colab
│   ├── 03_CLAM_annotation.ipynb         # CLAM model pipeline
│   └── 04_UNI_annotation.ipynb          # UNI model pipeline
│
├── scripts/
│   ├── filter_tnbc_patients.py          # Filter TNBC from TCGA-BRCA (19 conditions)
│   ├── extract_metadata.py              # Build metadata JSON per patient
│   ├── run_clam.py                      # Run CLAM annotation pipeline
│   └── run_uni.py                       # Run UNI annotation pipeline
│
├── results/
│   ├── tumor_summary.csv                # Per-patient tumor statistics
│   ├── model_comparison.csv             # CLAM vs UNI comparison table
│   └── figures/                         # Summary plots and heatmap examples
│
├── docs/
│   ├── methodology.md                   # Detailed methodology
│   └── data_dictionary.md               # Column descriptions for all CSVs
│
├── LICENSE                              # CC BY 4.0
└── README.md
```

---

## 🗂️ Dataset Description

### Cohort

| Property | Value |
|---|---|
| Cancer type | Triple-Negative Breast Cancer (TNBC) |
| Confirmed patients | **153** |
| Raw TCGA-BRCA patients screened | 1,108 |
| Total excluded | 955 |
| Source | TCGA-BRCA via cBioPortal + GDC |
| Slide type | FFPE H&E diagnostic slides (DX1) |
| Modality | Histopathology WSI only |

### Cohort Statistics

| Property | Value |
|---|---|
| Median age at diagnosis | 53 years |
| Most common histology | Infiltrating Ductal Carcinoma (131/153, 86%) |
| Most common stage | Stage IIA (72/153, 47%) |
| Deceased patients | 21/153 |
| Censored (living) | 132/153 |

### Stage Distribution

| Stage | Patients |
|---|---|
| Stage IA | 16 |
| Stage I | 13 |
| Stage IIA | 72 |
| Stage IIB | 25 |
| Stage II | 1 |
| Stage IIIA | 14 |
| Stage IIIB | 3 |
| Stage IIIC | 4 |
| Stage IV | 2 |

### Histologic Subtypes

| Subtype | Patients |
|---|---|
| Infiltrating Ductal Carcinoma | 131 |
| Infiltrating Lobular Carcinoma | 5 |
| Metaplastic Carcinoma | 5 |
| Medullary Carcinoma | 3 |
| Other / NOS | 9 |

### Clinical Variables Available

| Variable | Completeness |
|---|---|
| Patient ID | 153/153 |
| Diagnosis age | 153/153 |
| Overall survival (months) | 153/153 |
| Overall survival status | 153/153 |
| ER / PR / HER2 status | 153/153 |
| Cancer stage | 150/153 |
| Pathology report UUID (GDC) | 153/153 |
| Race / ethnicity | ~148/153 |

### Annotation Outputs Per Patient

| File | Description |
|---|---|
| `heatmap_CLAM.png` | Colored tumor probability map (CLAM model) |
| `heatmap_UNI.png` | Colored tumor probability map (UNI model) |
| `patch_labels_CLAM.csv` | Tile-level tumor scores from CLAM |
| `patch_labels_UNI.csv` | Tile-level tumor scores from UNI |
| `tumor_mask_CLAM.npy` | Binary pixel-level tumor mask (CLAM) |
| `tumor_mask_UNI.npy` | Binary pixel-level tumor mask (UNI) |
| `metadata.json` | Patient clinical info + imaging links |

---

## 🔬 Methodology

### Step 1 — Patient Selection

153 TNBC patients confirmed from TCGA-BRCA (1,108 total) using **19 strict clinical filters** including:

- ER Negative by IHC
- PR Negative by IHC
- HER2 Negative by ASCO/CAP criteria (IHC primary, FISH reflex)
- Primary tumor samples only
- Valid TCGA patient ID format
- No metastatic flag
- Diagnosis age 1–120
- ER/PR IHC staining < 10% (contradiction check)

### Step 2 — Slide Access

Histopathology slides are accessed via **GDC** using the `Pathology report uuid` in `TNBC_patient_slide_ids.csv`. Only DX1 (FFPE diagnostic) slides are used — frozen section slides are excluded.

```python
import requests, json

patient_id = "TCGA-A1-A0SK"
r = requests.post(
    "https://api.gdc.cancer.gov/files",
    headers={"Content-Type": "application/json"},
    data=json.dumps({
        "filters": {"op": "and", "content": [
            {"op": "=", "content": {"field": "cases.submitter_id", "value": patient_id}},
            {"op": "=", "content": {"field": "data_format", "value": "SVS"}},
        ]},
        "fields": "file_id,file_name",
        "size": "10"
    })
)
```

### Step 3 — AI Annotation Pipeline

Two AI models are run on each slide and results are compared:

**Model 1 — CLAM** Clustering-Constrained Attention Multiple Instance Learning. Widely used weakly supervised method for WSI tumor detection.

**Model 2 — UNI** Universal Pathology Foundation Model (Chen et al., Nature Medicine 2024). Trained on 100,000+ pathology slides.

### Step 4 — Quality Control

- HistoQC used for slide quality filtering
- Stain normalization applied before inference
- Manual spot-check on 10% of patients
- Completeness verified for all metadata fields

### Step 5 — Release

All outputs organized per patient and released on Zenodo with a permanent DOI under CC BY 4.0 license.

---

## 🚀 How to Use This Dataset

**Load clinical data**
```python
import pandas as pd

df = pd.read_csv('data/TNBC_master.csv')
df['OS_Months'] = pd.to_numeric(df['Overall Survival (Months)'], errors='coerce')
df['OS_Event']  = df['Overall Survival Status'].str.startswith('1').astype(int)
print(f"Patients: {len(df)}")
print(df[['Patient ID', 'OS_Months', 'OS_Event']].head())
```

**Load patient annotation**
```python
import numpy as np, json, pandas as pd

patient = 'TCGA-A1-A0SK'

with open(f'annotations/{patient}/metadata.json') as f:
    meta = json.load(f)

mask     = np.load(f'annotations/{patient}/tumor_mask_CLAM.npy')
patches  = pd.read_csv(f'annotations/{patient}/patch_labels_CLAM.csv')

print(f"Tumor area   : {mask.mean() * 100:.1f}%")
print(f"Total patches: {len(patches)}")
```

---

## 📊 Key Statistics

*(To be updated upon completion of annotation pipeline)*

| Metric | Value |
|---|---|
| Total patients | 153 |
| Slides annotated | — |
| Average tumor area (CLAM) | — |
| Average tumor area (UNI) | — |
| CLAM vs UNI agreement | — |
| Deceased patients | 21/153 |

---

## ⚠️ Ethical Statement

All data is sourced from The Cancer Genome Atlas (TCGA) via the NCI Genomic Data Commons (GDC), which operates under dbGaP open access policy. All patient data is fully de-identified. No new patient data was collected for this study. No additional ethical approval is required beyond compliance with TCGA data use policy.

Users of this dataset must:
- Not attempt to re-identify any patient
- Report any suspected data breaches
- Use data in accordance with applicable research ethics standards
- Cite this dataset appropriately in any publication

---

## 📥 Data Access

| Resource | Link |
|---|---|
| Histopathology slides (WSI) | [GDC Portal](https://portal.gdc.cancer.gov/) |
| IDC Slide Viewer | [IDC Viewer](https://viewer.imaging.datacommons.cancer.gov/) |
| Zenodo release | Coming soon |
| DOI | Coming soon |

Primary imaging data remains hosted on GDC. This repository contains derived annotations and structured metadata only.

---

## 📖 Citation

If you use this dataset please cite:

```
Sajjad F, Ibrahim J. (2025). TNBC-Histopathology-Dataset: Structured Annotation
and Metadata Integration of Triple-Negative Breast Cancer Histopathology Slides.
GitHub. https://github.com/fahadsajjad299/TNBC-Histopathology-Dataset
```
