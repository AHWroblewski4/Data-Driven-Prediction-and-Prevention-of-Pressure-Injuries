
# Beyond Braden: HAPI Prediction Data Pipeline

This repository contains the complete end-to-end data engineering and modeling pipeline for the **Hospital-Acquired Pressure Injury (HAPI)** prediction project. 

The project processes raw clinical data from the **MIMIC-IV** database, engineers features across multiple clinical domains (demographics, medications, labs, notes), and trains machine learning models to identify at-risk patients.
## Link to Presentation: https://youtu.be/XIPB1rXmFLY

## Project Architecture

The pipeline follows a modular structure where specific domains are processed in individual notebooks before being merged into the final analytic dataset.

### Data Flow Overview
Raw Inputs (MIMIC CSVs) ➔ Processing Notebooks (.ipynb) ➔ Intermediate Feature Sets (CSVs) ➔ Final Modeling

---

## Execution Order

To reproduce the dataset, run the notebooks in the following numerical order. Ensure the raw MIMIC-IV CSV files are in your input directory before starting.

### Phase 1: Cohort & Label Generation
* **0.Build_HAPI_Labels.ipynb**
    * **Inputs:** `discharge.csv`, `admissions.csv`, `diagnoses_icd.csv`
    * **Action:** Identifies the target variable (HAPI) and defines the study cohort.
    * **Output:** `hospitalwide_hapi_labels.csv`

### Phase 2: Clinical Feature Engineering
* **1.Demographicsfeatures.ipynb**
    * **Inputs:** `patients.csv`, `admissions.csv`
    * **Output:** `demographics_feat.csv`

* **2.LOSfeatures.ipynb**
    * **Inputs:** `transfers.csv`
    * **Action:** Calculates Length of Stay metrics.
    * **Output:** `los_feat.csv`

* **3.TransferFeatures.ipynb**
    * **Inputs:** `inputevents.csv`, `transfers.csv`
    * **Output:** `icu_transfer.csv`

* **4.I&Ofeatures.ipynb** (Input/Output Events)
    * **Inputs:** `inputevents.csv`, `outputevents.csv`
    * **Output:** `io_feat.csv`

* **5.ICUProceduresFeatures.ipynb**
    * **Inputs:** `icustays.csv`, `procedureevents.csv`
    * **Output:** `icu_procedures_feat.csv`

* **7.Discharge and RadiologynoteFeatures.ipynb**
    * **Inputs:** `radiology.csv`, `discharge.csv`
    * **Action:** Processes unstructured clinical notes.
    * **Output:** `feat_notes.csv`

### Phase 3: Medical Coding & Orders
* **8.ICD_Correlation.ipynb**
    * **Inputs:** `diagnoses_icd.csv`
    * **Output:** `diagnoses_feat.csv`

* **9.Procedure_feat.ipynb**
    * **Inputs:** `procedures_icd.csv`
    * **Output:** `procedures_feat.csv`

* **10.POEfeatures.ipynb** (Provider Order Entry)
    * **Inputs:** `poe.csv`, `poe_detail.csv`
    * **Output:** `poe_detail_feat.csv`

### Phase 4: Medication Pipeline
* **11.Prescription_Features.ipynb**
    * **Inputs:** `prescriptions.csv`
    * **Output:** `prescriptions_feat.csv`

* **12.EMAR_Features.ipynb**
    * **Inputs:** `emar.csv`, `emar_detail.csv`
    * **Output:** `emar_feat.csv`

* **13.Pharm_Features.ipynb**
    * **Inputs:** `pharmacy.csv`
    * **Output:** `pharmacy_feat.csv`

* **14.Merge_Pharm.ipynb**
    * **Inputs:** Outputs from Steps 11, 12, and 13.
    * **Action:** Consolidates all medication data into a master feature set.
    * **Output:** `medications_master_feat.csv`

### Phase 5: Final Data Assembly (Master Merge)
* **15.master_merge_analytic.inypb**
    * **Inputs:** **All** `*_feat.csv` files (up to 18 tables), including `hospitalwide_hapi_labels.csv`.
    * **Action:** Merges all feature tables using a robust, error-checking `left merge` on `hadm_id`.
    * **Output:** `FINAL_HAPI_ANALYTIC.csv`

## Final Modeling

Once Step 15 is complete and all intermediate CSVs are merged into the final analytic file, run the model training pipeline.

**File:** `hapi_model_pipeline.ipynb`

This script performs:
1.  **Leakage Cleaning:** Automatically removes features highly correlated with the label (|r| > 0.95) or containing future information.
2.  **Training:** Trains a Random Forest (Baseline) and XGBoost classifier.
3.  **Evaluation:** Outputs confusion matrices and performance metrics (ROC-AUC, Recall, Precision).

## Requirements

* Python 3.8+
* Jupyter Notebook
* Access to MIMIC-IV Database (Credentialed access required)

### Libraries
```bash
pip install pandas numpy scikit-learn xgboost matplotlib seaborn

