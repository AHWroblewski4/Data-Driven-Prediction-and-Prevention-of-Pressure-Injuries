# Project Goal

The goal of this project is to build a machine-learning model that predicts Hospital-Acquired Pressure Injuries (HAPIs) using a combination of:

-Structured EHR data

-Unstructured clinical notes

-Medication administration records

-ICU procedures, transfers, and I/O

-ICD diagnosis and procedure codes

This project uses MIMIC-IV data and simulates how a real hospital could build a predictive analytics pipeline for early HAPI detection.

## Data Pipeline Overview

This project uses a modular pipeline.  
Each raw MIMIC-IV table becomes a structured *_feat.csv file.  
All features are merged into the final analytic dataset.  

## Steps:

### 1. HAPI Label Construction
-HAPI_STRUCTURED – ICD-9/10 pressure ulcer codes

-HAPI_UNSTRUCTURED – NLP keyword detection in discharge notes

-HAPI_FINAL – Combined label used for modeling

**Output:** hospitalwide_hapi_labels.csv

### 2. Demographics Features

-Age

-Gender

-Admission type

Output: demographics_feat.csv
