# Stroke Data Cleaning, Preprocessing & Exploratory Data Analysis

[![R](https://img.shields.io/badge/R-Programming-276DC3?logo=r&logoColor=white)](https://www.r-project.org/)
![Project Type](https://img.shields.io/badge/Project-Introduction%20to%20Data%20Science-blue)
![Status](https://img.shields.io/badge/Status-Completed-success)

A complete **data cleaning, preprocessing, and exploratory data analysis (EDA)** workflow developed in **R** using the Stroke Prediction Dataset.

This repository was created as a **Mid-Term Project for the Introduction to Data Science course** at **American International University-Bangladesh (AIUB)**. The project focuses on identifying realistic data-quality problems, applying appropriate cleaning and preprocessing techniques, and exploring meaningful patterns in an analysis-ready healthcare dataset.

> **Scope note:** This phase of the project focuses on data preparation and exploratory analysis. Predictive modeling and stroke classification are intentionally outside the scope of this repository.

---

## Project Overview

Real-world datasets often contain missing values, inconsistent labels, invalid codes, incorrect data types, and outliers. These issues can significantly reduce the reliability of any downstream analysis.

For this project, the original Stroke Prediction Dataset was deliberately modified to introduce realistic data-quality problems. The objective was to build and document a structured workflow that transforms the dirty dataset into a clean, consistent, and analysis-ready dataset.

The project covers:

- Dataset inspection and understanding
- Missing-value analysis
- Duplicate-record checking
- Inconsistent categorical-value detection
- Category standardization
- Invalid-value correction
- Outlier detection and treatment
- Median and mode imputation
- Data-type conversion
- Min-Max normalization
- Data filtering
- Descriptive statistics
- Group-based analysis
- Correlation analysis
- Export of the cleaned dataset

---

## Academic Information

| Item | Details |
|---|---|
| **University** | American International University-Bangladesh (AIUB) |
| **Department** | Department of Computer Science |
| **Faculty** | Faculty of Science & Technology |
| **Course** | Introduction to Data Science |
| **Project Type** | Mid-Term Project |
| **Section** | K |
| **Group** | 04 |
| **Semester** | Summer 2025-26 |
| **Supervisor** | Kamrun Naher Koli |

### Group Members

- MD. Murad Hasan
- Mahbuba Nasrin
- Atia Chowdhury
- Najiat Islam Rishad

---

## Dataset

The project uses the **Stroke Prediction Dataset** originally published on Kaggle by **fedesoriano**.

**Source:**  
https://www.kaggle.com/datasets/fedesoriano/stroke-prediction-dataset

### Dataset Size

- **5,110 patient records**
- **12 original variables**
- **10 predictor variables**
- **1 binary target variable (`stroke`)**
- **1 patient identifier (`id`)**

### Variables

| Variable | Description |
|---|---|
| `id` | Unique patient identifier |
| `gender` | Patient gender |
| `age` | Patient age in years |
| `hypertension` | Whether the patient has hypertension |
| `heart_disease` | Whether the patient has heart disease |
| `ever_married` | Whether the patient has ever been married |
| `work_type` | Employment/work category |
| `Residence_type` | Urban or Rural residence |
| `avg_glucose_level` | Average blood glucose level |
| `bmi` | Body Mass Index |
| `smoking_status` | Smoking history/status |
| `stroke` | Stroke occurrence: No / Yes |

---

## Data Quality Problems

A copy of the original dataset was intentionally modified to simulate realistic data-cleaning challenges.

The dirty dataset contains issues such as:

| Problem | Example / Description |
|---|---|
| **Missing values** | Missing entries in gender, age, hypertension, heart disease, marital status, and BMI |
| **Inconsistent categories** | `Yes`, `yes`, `Y`, `1`, `Married`, `No`, `no`, `N` |
| **Invalid gender entries** | `unknown` |
| **Invalid binary codes** | Values outside `0` and `1` in hypertension and heart disease |
| **Age outliers** | Negative ages and unrealistically large values |
| **Duplicates** | Checked programmatically; no duplicate rows were found |

The purpose of introducing these issues was to demonstrate a complete cleaning and preprocessing workflow on realistic data problems.

---

## Data Cleaning & Preprocessing Workflow

### 1. Dataset Inspection

The dataset was imported using `readr::read_csv()` and examined using:

```r
dim(stroke_data)
str(stroke_data)
summary(stroke_data)
```

This step was used to understand dataset dimensions, variable types, value ranges, and initial data-quality problems.

### 2. Missing-Value Analysis

Missing values were counted and converted into percentages for every column.

```r
colSums(is.na(stroke_data))

round(
  colSums(is.na(stroke_data)) / nrow(stroke_data) * 100,
  2
)
```

A bar plot was also used to visualize missing values across variables.

### 3. Duplicate Detection

Duplicate observations were checked using:

```r
sum(duplicated(stroke_data))
```

No duplicate rows were detected.

### 4. Standardizing `ever_married`

Different representations of the same category were converted into consistent `Yes` and `No` values.

Examples:

```text
yes, Y, 1, Married  -> Yes
no, N               -> No
```

### 5. Handling Invalid Gender Values

The invalid value `unknown` was treated as missing before imputation.

### 6. Age Outlier Detection

Age outliers were identified using the **Interquartile Range (IQR)** method.

```r
Q1 <- quantile(stroke_data$age, 0.25, na.rm = TRUE)
Q3 <- quantile(stroke_data$age, 0.75, na.rm = TRUE)
IQR_age <- IQR(stroke_data$age, na.rm = TRUE)

lower_bound <- Q1 - 1.5 * IQR_age
upper_bound <- Q3 + 1.5 * IQR_age
```

Domain knowledge was also applied because negative ages are impossible even when they are not captured by a purely statistical rule.

Invalid ages were converted to missing values and replaced using the **median age**.

### 7. Hypertension & Heart Disease

Both variables are binary and should contain only `0` or `1`.

Invalid values were converted to `NA` and replaced using the **mode**, which is appropriate for categorical binary variables.

### 8. Missing Categorical Values

Missing values in:

- `gender`
- `ever_married`

were replaced using their respective **mode**.

### 9. Missing BMI Values

Missing BMI values were replaced using the **median BMI**.

Median imputation was chosen because it is more robust to skewed distributions and extreme values than the mean.

### 10. Final Missing-Value Check

A final validation confirmed that no missing values remained after cleaning.

### 11. Type Conversion

The following binary variables were converted from numeric values into meaningful factor labels:

- `hypertension`
- `heart_disease`
- `stroke`

```r
levels = c(0, 1)
labels = c("No", "Yes")
```

### 12. Min-Max Normalization

Min-Max normalization was applied to:

- `age`
- `avg_glucose_level`
- `bmi`

using:

```r
normalize <- function(x) {
  (x - min(x)) / (max(x) - min(x))
}
```

The normalized variables were stored in separate columns so the original measurements remained available for interpretation.

### 13. High-Risk Data Filtering

A focused subset was created for patients:

- aged **50 years or older**, and
- with **hypertension**

```r
high_risk <- filter(
  stroke_data,
  age >= 50 & hypertension == "Yes"
)
```

The resulting subset contained **384 records**.

---

## Exploratory Data Analysis

After cleaning and preprocessing, several EDA tasks were performed.

### Descriptive Statistics

Summary statistics were generated for:

- Age
- Average glucose level
- BMI
- Stroke status

### Average Glucose Level by Stroke Status

Patients in the stroke group had a higher average glucose level than patients in the non-stroke group.

| Stroke Status | Mean Glucose Level |
|---|---:|
| No Stroke | **104.80 mg/dL** |
| Stroke | **132.54 mg/dL** |

### Average Age by Stroke Status

The stroke group was also substantially older on average.

| Stroke Status | Mean Age |
|---|---:|
| No Stroke | **42.1 years** |
| Stroke | **65.8 years** |

These are descriptive associations observed in this dataset and should not be interpreted as causal relationships.

### Stroke-Class Distribution

The target variable is strongly imbalanced:

- **4,861** patients: No Stroke
- **249** patients: Stroke
- Stroke cases represent approximately **4.9%** of the dataset

This imbalance would be an important consideration in any future predictive-modeling phase.

### BMI by Smoking Status

BMI distributions were compared across smoking-status categories using boxplots and group-level mean and standard deviation.

The three recorded smoking-behavior groups had broadly similar mean BMI values, while the `Unknown` category showed a lower average BMI.

### Correlation Analysis

Pearson correlations among the main numerical variables were:

| Variables | Correlation |
|---|---:|
| Age & BMI | **0.31** |
| Age & Average Glucose Level | **0.24** |
| Average Glucose Level & BMI | **0.17** |

The relationships are positive but weak to moderate, suggesting that the variables provide largely distinct information.

---

## Key Results

The final cleaning pipeline successfully:

- Standardized inconsistent categorical values
- Corrected invalid binary codes
- Detected and handled unrealistic age values
- Imputed missing numerical and categorical values
- Preserved all patient records instead of deleting rows unnecessarily
- Converted categorical variables into meaningful factor representations
- Added normalized numerical features
- Produced a dataset with **no remaining missing values**
- Created an analysis-ready clean dataset
- Identified notable descriptive differences between stroke and non-stroke groups

---

## Repository Structure

```text
stroke-data-cleaning-preprocessing-eda/
│
├── Data/
│   ├── healthcare-dataset-stroke-data-dirty.csv
│   └── healthcare-dataset-stroke-data-clean.csv
│
├── Document/
│   └── IDS-MidTerm-Project-Group-4-Section-K.pdf
│
├── scripts/
│   └── data_cleaning.R
│
├── .gitignore
├── Stroke_Data_Cleaning.Rproj
└── README.md
```

### File Description

| File / Folder | Purpose |
|---|---|
| `Data/healthcare-dataset-stroke-data-dirty.csv` | Dataset containing the intentionally introduced data-quality problems |
| `Data/healthcare-dataset-stroke-data-clean.csv` | Final cleaned and preprocessed dataset |
| `scripts/data_cleaning.R` | Complete R workflow for cleaning, preprocessing, filtering, and EDA |
| `Document/IDS-MidTerm-Project-Group-4-Section-K.pdf` | Full academic project report |
| `Stroke_Data_Cleaning.Rproj` | RStudio project file |
| `.gitignore` | Excludes local R/RStudio session files from Git |

---

## Technologies & Tools

- **R**
- **RStudio**
- **readr**
- **dplyr**
- **Base R Statistics**
- **Base R Visualization**
- **Git**
- **GitHub**

---

## How to Run the Project

### 1. Clone the Repository

```bash
git clone https://github.com/mhtamim136/stroke-data-cleaning-preprocessing-eda.git
```

### 2. Open the Project

Open:

```text
Stroke_Data_Cleaning.Rproj
```

in RStudio.

### 3. Install Required Packages

```r
install.packages(c("readr", "dplyr"))
```

### 4. Run the Analysis

From the repository root:

```r
source("scripts/data_cleaning.R")
```

The script reads the dirty dataset from the `Data/` directory, performs the cleaning and analysis workflow, and generates/uses the cleaned dataset for further analysis.

---

## Project Report

The complete academic report, including code outputs, visualizations, explanations, and interpretations, is available here:

[`Document/IDS-MidTerm-Project-Group-4-Section-K.pdf`](Document/IDS-MidTerm-Project-Group-4-Section-K.pdf)

---

## Future Work

Predictive modeling was outside the scope of the mid-term project. Possible future extensions include:

- Encoding categorical features for machine learning
- Train/test splitting
- Addressing target-class imbalance
- Logistic Regression
- Decision Trees / Random Forest
- Support Vector Machines
- Model evaluation using Precision, Recall, F1-score, ROC-AUC, and confusion matrices
- Feature-importance analysis

These items are proposed future directions and are **not part of the current implementation**.

---

## Important Note

This repository is an **academic data science project** created for educational purposes.

The analysis demonstrates data-cleaning, preprocessing, and exploratory-analysis techniques. It is **not a clinical decision-support system** and should not be used for medical diagnosis, treatment, or individual stroke-risk assessment.

---

## Acknowledgment

This project was completed as part of the **Introduction to Data Science** course at **American International University-Bangladesh (AIUB)** under the supervision of **Kamrun Naher Koli**.

Dataset credit: **fedesoriano — Stroke Prediction Dataset, Kaggle**.

---

## Repository

**GitHub:**  
https://github.com/mhtamim136/stroke-data-cleaning-preprocessing-eda
