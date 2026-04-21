# Data Directory

This folder contains the patient cohort, clinical metadata, and slide identifiers for the **TCGA-BRCA Triple-Negative Breast Cancer (TNBC)** dataset used in this project.

---

## What is TNBC?

Triple-Negative Breast Cancer (TNBC) is defined by the **absence of all three receptors**:
- Estrogen Receptor (ER) — Negative
- Progesterone Receptor (PR) — Negative
- HER2 — Negative (by IHC and/or FISH)

It is the most aggressive subtype of breast cancer and has no targeted therapy, making it an important focus for AI-based prognosis research.

---

## Source

- **Dataset:** TCGA-BRCA (The Cancer Genome Atlas — Breast Cancer)
- **Downloaded from:** [cBioPortal](https://www.cbioportal.org/)
- **Imaging:** NCI Genomic Data Commons (GDC) — whole-slide SVS images
- **Total patients in raw TSV:** 1,108
- **Confirmed TNBC patients:** 153

---

## Cohort Summary

| Property | Value |
|---|---|
| Confirmed TNBC patients | 153 |
| Median age at diagnosis | 53 years |
| Sample type | Primary tumor only |
| Predominant histology | Infiltrating Ductal Carcinoma (86%) |
| Most common stage | Stage IIA (47%) |
| Overall survival events (deceased) | 21 |
| Censored (living) | 132 |

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

---

## Files

### `TNBC_confirmed_patients.csv`
Full clinical data for all 153 confirmed TNBC patients. Contains all original columns from the cBioPortal TSV plus FFPE flag and derived fields.

### `TNBC_master.csv`
Curated master table with the most relevant columns for AI training — receptor status, survival outcomes, stage, histology, treatment, and genomics. **Use this file for model training.**

### `TNBC_patient_slide_ids.csv`
Patient IDs, Sample IDs, Pathology Report UUIDs, AJCC stage, and histologic type. Use the `Pathology report uuid` column to query the GDC for whole-slide SVS image downloads.

### `TNBC_exclusion_log.csv`
Audit trail showing why each of the 955 excluded patients was removed, with the specific filter condition that caused their exclusion.

### `brca_tcga_clinical_data_from_cbioportal.tsv`
Raw unfiltered TCGA-BRCA clinical data downloaded directly from cBioPortal. This is the original input file — do not edit it.

---

## Exclusion Criteria

Patients were excluded from the cohort for the following reasons:

| Condition | Excluded |
|---|---|
| ER not confirmed Negative | 857 |
| HER2 not confirmed Negative (ASCO/CAP criteria) | 60 |
| PR not confirmed Negative | 18 |
| Sample type is not Primary | 7 |
| Cancer type missing | 5 |
| Metastatic tumor indicator = YES | 5 |
| Cancer type is not Breast Cancer | 3 |

---

## How to Use

```python
import pandas as pd

# Load master clinical data
df = pd.read_csv('TNBC_master.csv')

# Survival labels
df['OS_Months'] = pd.to_numeric(df['Overall Survival (Months)'], errors='coerce')
df['OS_Event']  = df['Overall Survival Status'].str.startswith('1').astype(int)

# Get slide UUIDs for GDC download
slides = pd.read_csv('TNBC_patient_slide_ids.csv')
print(slides[['Patient ID', 'Pathology report uuid']].head())
```

---

## Receptor Status Definition Used

| Receptor | Criterion |
|---|---|
| ER | IHC = Negative |
| PR | IHC = Negative |
| HER2 | IHC = Negative **or** IHC Equivocal + FISH = Negative |

Patients with missing or equivocal HER2 data and no FISH reflex result were excluded as `Incomplete-Data` — not classified as TNBC.
