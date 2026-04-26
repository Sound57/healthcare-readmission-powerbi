# 🏥 Hospital Readmission Analytics — Power BI Dashboard

![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![DAX](https://img.shields.io/badge/DAX-534AB7?style=for-the-badge&logoColor=white)
![Status](https://img.shields.io/badge/Status-Completed-1D9E75?style=for-the-badge)

An end-to-end healthcare analytics project analyzing **101,766 diabetic patient admissions** across 130 US hospitals. Built to demonstrate skills across Data Analysis, Business Intelligence, and Predictive Modelling.
<img width="1118" height="630" alt="image" src="https://github.com/user-attachments/assets/23a43e54-53d8-4c61-8b03-6c3ff2bae6bd" />
<img width="1122" height="632" alt="image" src="https://github.com/user-attachments/assets/5c1fe0a1-1838-4499-94b5-6b0210166392" />
<img width="1116" height="632" alt="image" src="https://github.com/user-attachments/assets/77896a85-1758-45d8-afdc-f82f6332a02b" />


> 📊 **[View Live Dashboard on Power BI Service](#)**

---

## 📌 Problem Statement

Hospital readmissions within 30 days are a major quality and cost concern — hospitals face financial penalties for high readmission rates under CMS regulations. This project identifies **which patients are at highest risk of readmission** and what clinical and demographic factors drive that risk, enabling hospitals to allocate intervention resources more effectively.

---

## 📁 Dataset

| Detail | Info |
|--------|------|
| Source | [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/296/diabetes+130-us+hospitals+for+years+1999-2008) |
| Records | 101,766 patient admissions |
| Features | 50 columns (demographics, diagnoses, medications, lab results) |
| Period | 1999 – 2008 |
| Hospitals | 130 US hospitals |

**Files used:**
- `diabetic_data.csv` — main patient admission records
- `IDS_mapping.csv` — lookup tables for admission type, discharge type, admission source

---

## 🛠️ Tools & Technologies

| Tool | Purpose |
|------|---------|
| Power BI Desktop | Dashboard development, DAX measures, data modelling |
| Power Query (M) | Data cleaning and transformation |
| DAX | Custom KPI measures and advanced calculations |
| Python (scikit-learn) | Logistic regression model for readmission prediction |
| GitHub | Version control and project documentation |

---

## 🗂️ Project Architecture

### Star Schema Data Model

```
fact_Admissions (diabetic_data)
    ├── dim_Patient          (patient_nbr)
    ├── dim_AdmissionType    (admission_type_id)
    ├── dim_DischargeType    (discharge_disposition_id)
    └── dim_AdmissionSource  (admission_source_id)
```

All dimension tables connect to the fact table via one-to-many relationships, enabling accurate cross-filtering across all dashboard pages.

---

## 🧹 Data Cleaning (Power Query)

| Issue Found | Fix Applied |
|-------------|-------------|
| `?` used as null marker across 6 columns | Replaced all `?` with null using Replace Values |
| `weight` column — 97% missing (98,569 nulls) | Column dropped |
| `readmitted` has 3 values: NO / <30 / >30 | Created binary `Readmitted_30Days` (Yes/No) column |
| Age stored as brackets: `[50-60)` | Created `Age_Midpoint` numeric column for sorting |
| 23 medication columns with text values | Created `Active_Med_Count` numeric summary column |
| IDS_mapping — 3 tables stacked in one CSV | Split into 3 separate dimension tables using M code |

---

## 📊 Dashboards

### Dashboard 1 — Executive Overview
The C-suite view with hospital-wide KPIs and trend analysis.

**Visuals:**
- 5 KPI cards (Total Admissions, Readmission Rate %, Avg Length of Stay, High Risk Patients, Insulin Usage Rate %)
- Readmission rate by age group (bar chart)
- Admissions by type (donut chart)
- Readmission breakdown — Early / Late / None (donut chart)
- Readmission by discharge destination (horizontal bar)
- Slicers: Gender, Age Group, Readmission Status

---

### Dashboard 2 — Patient Risk Analysis
Identifies which patient segments carry the highest readmission risk.

**Visuals:**
- Scatter plot: Treatment intensity vs length of stay (by age bubble)
- Matrix heatmap: Patient count by age × readmission status (conditional formatting)
- Key Influencers AI visual: Factors that most increase readmission probability
- Readmission rate vs hospital average (above/below average bar chart)
- Drillthrough: Click any age group → detailed breakdown page

---

### Dashboard 3 — Predictive Insights
ML model integration showing predicted readmission risk and feature importance.

**Visuals:**
- Python visual: Feature importance from logistic regression model
- What-If parameter: Simulate readmission rate reduction scenarios
- Model accuracy KPI cards (Accuracy, Precision, Recall, AUC-ROC)
- Predicted vs actual readmission comparison

---

## 📐 DAX Measures (18 custom measures)

```dax
-- Core KPIs
Total Admissions     = COUNTROWS(diabetic_data)
Readmitted 30Days    = CALCULATE(COUNTROWS(diabetic_data), diabetic_data[Readmitted_30Days] = "Yes")
Readmission Rate %   = DIVIDE([Readmitted 30Days], [Total Admissions]) * 100
Avg Length of Stay   = AVERAGE(diabetic_data[time_in_hospital])

-- Risk measures
High Risk Patients   = CALCULATE(COUNTROWS(diabetic_data), diabetic_data[number_inpatient] >= 2)
Insulin Usage Rate % = DIVIDE(CALCULATE(COUNTROWS(diabetic_data), diabetic_data[insulin] <> "No"), [Total Admissions]) * 100

-- Advanced DAX
Readmission vs Average = [Readmission Rate %] - CALCULATE([Readmission Rate %], ALL(dim_Patient[age]))
Simulated Readmission Rate = [Readmission Rate %] * (1 - 'Reduction Factor'[Reduction Factor Value])
```

---

## 🔍 Key Findings

1. **Overall 30-day readmission rate is 11.16%** (11,357 out of 101,766 admissions)

2. **Age 20-30 has the highest readmission rate at 14.2%** — counter-intuitive but likely driven by socioeconomic factors and lower treatment compliance in younger diabetic patients

3. **Patients aged 80-90 follow at 12.1%** — high absolute numbers due to comorbidities and longer hospital stays

4. **53.4% of patients were on insulin** — indicating predominantly Type 1 or advanced Type 2 diabetes in this cohort

5. **14,615 patients (14.4%) are classified as high risk** (2+ prior inpatient visits) — the Key Influencers analysis confirms prior inpatient visits as the single strongest readmission predictor

6. **34.9% were readmitted after 30 days** — suggesting significant late-stage chronic disease management gaps post-discharge

---

## 💡 Business Recommendations

Based on the analysis, three targeted interventions could reduce readmission rates:

1. **High-Risk Monitoring Program** — Flag patients with 2+ prior inpatient visits at admission and assign a dedicated care coordinator. This group drives the majority of readmissions.

2. **Post-Discharge Follow-Up for 20-30 Age Group** — The highest readmission rate age bracket (14.2%) likely benefits from structured 7-day post-discharge check-ins given lower autonomous follow-up behaviour.

3. **A1C Testing Protocol** — Only a fraction of patients had A1C results recorded. Systematic A1C testing at every admission enables better glycemic management, directly linked to reduced readmission.

---

## 🤖 ML Model (Logistic Regression)

Built in Python using scikit-learn and embedded as a Python visual in Power BI.

**Features used:**
`time_in_hospital`, `num_medications`, `number_inpatient`, `number_emergency`, `Active_Med_Count`, `Age_Midpoint`, `num_lab_procedures`

**Results:**

| Metric | Score |
|--------|-------|
| Accuracy | ~79% |
| Precision | ~68% |
| Recall | ~52% |
| AUC-ROC | ~0.74 |

> Note: Recall is intentionally prioritised over precision in this context — in healthcare, missing a high-risk patient (false negative) carries greater cost than a false alarm (false positive).

---

## 📂 Repository Structure

```
healthcare-readmission-powerbi/
│
├── Healthcare_Readmission_Analytics.pbix   ← Main Power BI file
├── README.md                                ← This file
│
├── data/
│   ├── diabetic_data.csv                    ← Source dataset (UCI)
│   └── IDS_mapping.csv                      ← Lookup/mapping tables
│
└── screenshots/
    ├── dashboard1_executive_overview.png
    ├── dashboard2_patient_risk.png
    └── dashboard3_predictive_insights.png
```

---

## 🚀 How to Run This Project

1. Clone or download this repository
2. Install [Power BI Desktop](https://powerbi.microsoft.com/desktop/) (free)
3. Open `Healthcare_Readmission_Analytics.pbix`
4. If data source path errors appear: **Home → Transform Data → Data source settings → Change source** → point to the `data/` folder
5. Click **Refresh** to reload all data

**For the Python visual (Dashboard 3):**
```bash
pip install scikit-learn pandas numpy matplotlib
```
Then in Power BI: **File → Options → Python scripting** → set your Python path.

---

## 👤 Author

**[Your Name]**
- LinkedIn: [linkedin.com/in/yourprofile](#)
- Email: your.email@gmail.com

---

## 📄 License

This project uses the UCI Diabetes 130-US Hospitals dataset which is publicly available for research and educational use. See [UCI ML Repository](https://archive.ics.uci.edu/dataset/296) for full dataset license details.

---

*Built as a portfolio project demonstrating Power BI, DAX, data modelling, and ML integration skills.*
