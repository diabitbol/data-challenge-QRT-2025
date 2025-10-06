# 🧬 QRT Data Challenge 2025 – Blood Cancer Survival Prediction

## 📖 Context
In recent years, the medical sector has increasingly adopted methods based on large-scale health data analysis, especially for prognosis and treatment of complex diseases such as cancer.  
Predictive models have transformed patient care by enabling more personalized and effective treatment strategies. These advances are particularly valuable in oncology, where accurate prediction models can significantly improve both the **quality** and **timing** of therapeutic decisions.

---

## 🎯 Objective
In partnership with **Institut Gustave Roussy**, the **QRT Data Challenge 2025** focuses on predicting the **risk of death** for patients diagnosed with a blood cancer — specifically, a subtype of **adult myeloid leukemia**.

For each patient, the outcome of interest is the **Overall Survival (OS)**, defined as the period between the initial diagnosis and either the patient’s death or their last recorded follow-up.

### Why is this important?
Estimating a patient’s prognosis is essential for adapting therapeutic approaches:
- **Low-risk patients** can receive supportive therapies aimed at improving blood parameters and overall quality of life.  
- **High-risk patients** can be prioritized for intensive treatment options such as **hematopoietic stem cell transplantation**.

Accurate risk predictions can lead to:
- Better clinical decisions,  
- Improved patient outcomes,  
- More efficient allocation of hospital resources.

This challenge gives participants the unique opportunity to work with **real-world clinical data** from **24 medical centers**, applying data science to a meaningful medical application.

---

## 🧪 Dataset Description

The dataset is composed of two ZIP archives and one CSV file:

| File | Description |
|------|--------------|
| `X_train.zip` | Training input data |
| `X_test.zip` | Test input data |
| `Y_train.csv` | Target labels (outcomes) |

- Training set: **3,323 patients**  
- Test set: **1,193 patients**

Each patient has a unique identifier `ID` linking the **Clinical Data**, **Molecular Data**, and **Y_train** files.

---

### 🧫 Clinical Data (one row per patient)

| Column | Description |
|---------|-------------|
| `ID` | Unique patient identifier |
| `CENTER` | Clinical center where the patient was treated |
| `BM_BLAST` | Percentage of bone marrow blasts (abnormal blood cells) |
| `WBC` | White Blood Cell count (G/L) |
| `ANC` | Absolute Neutrophil Count (G/L) |
| `MONOCYTES` | Monocyte count (G/L) |
| `HB` | Hemoglobin level (g/dL) |
| `PLT` | Platelet count (G/L) |
| `CYTOGENETICS` | Karyotype description following ISCN standards (e.g., `46,XX`, `46,XY`) |

Abnormalities such as **monosomy 7** (loss of chromosome 7) are associated with high-risk disease.

---

### 🧬 Genetic Molecular Data (one row per somatic mutation per patient)

| Column | Description |
|---------|-------------|
| `ID` | Unique patient identifier |
| `CHR`, `START`, `END` | Genomic location of the mutation |
| `REF`, `ALT` | Reference and alternate nucleotides |
| `GENE` | Affected gene |
| `PROTEIN_CHANGE` | Impact on the encoded protein |
| `EFFECT` | Classification of the mutation effect |
| `VAF` | Variant Allele Fraction (proportion of cells carrying the mutation) |

---

### 🎯 Target Variables (in `Y_train.csv`)

| Column | Description |
|---------|-------------|
| `OS_YEARS` | Overall survival time in years since diagnosis |
| `OS_STATUS` | Survival status (1 = deceased, 0 = alive at last follow-up) |

---

## 📈 Challenge Task

The goal is to **predict a continuous risk score** for each patient, reflecting their **likelihood of death**.  
The scale of the score does **not** matter — only the **relative ranking** between patients.

> For two patients *i* and *j*, a lower predicted risk for *i* than for *j* means that *i* is expected to survive longer than *j*.

The submission file must contain:

| Column | Description |
|---------|-------------|
| `ID` | Patient identifier |
| `risk_score` | Predicted risk of death |

📁 Example:  
A random submission file is provided in the “Files” section.

---

## 🧮 Evaluation Metric — IPCW-C-index

The official evaluation metric is the **Inverse Probability of Censoring Weighted Concordance Index (IPCW-C-index)**, computed using [`scikit-survival`](https://scikit-survival.readthedocs.io/).

### 🔹 Concordance Index (C-index)
The **C-index** measures how well a model can correctly order survival times — i.e., how often predicted risks correspond to actual outcomes.

For comparable patient pairs (*i*, *j*) with *Tᵢ < Tⱼ*, the pair is **concordant** if:
\[
Rᵢ > Rⱼ
\]
where *R* is the predicted risk.

\[
C = \frac{\text{Number of Concordant Pairs}}{\text{Total Number of Comparable Pairs}}
\]

| Score | Interpretation |
|--------|----------------|
| **1.0** | Perfect ranking |
| **0.5** | Random predictions |

### 🔹 IPCW Extension
The **IPCW-C-index** extends the C-index to handle **right-censored data**, i.e. patients whose full survival time is unknown because they were still alive at the last follow-up.  
Each pair is weighted according to the probability that it was observed (not censored).

The metric is **truncated at 7 years** to ensure comparability.

---

## ⚙️ Submission Format

Your submission must be a CSV file with the following structure:

