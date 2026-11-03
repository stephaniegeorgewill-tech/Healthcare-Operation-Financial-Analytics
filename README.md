# Executive Healthcare Operations & Financial Analytics Dashboard

![Power BI](https://img.shields.io/badge/Power_BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![SQL](https://img.shields.io/badge/SQL-336791?style=for-the-badge&logo=postgresql&logoColor=white)
![DAX](https://img.shields.io/badge/DAX-005F73?style=for-the-badge&logo=microsoft&logoColor=white)
![Data Governance](https://img.shields.io/badge/Data_Governance-800020?style=for-the-badge)

An end-to-end healthcare analytics solution built to bridge operational patient dynamics with financial claims performance. Designed with a custom 2x2 executive layout, custom KPI cards, strict DAX data modeling, and comprehensive SQL validation governance.

---

## 📌 Project Overview & Objectives

Healthcare executives often struggle to reconcile operational volume spikes with net revenue performance due to fragmented reporting. This project delivers a high-end, custom Executive Dashboard moving beyond default templates to track operational efficiency alongside financial reimbursement ratios.

### Key Objectives:
- **Operational Volume Tracking:** Monitor encounter dynamics, patient distribution across care settings, and mean encounter duration.
- **Financial Claims Analysis:** Evaluate gross billed costs (`TOTAL_CLAIM_COST`) versus actual third-party insurer payouts (`PAYER_COVERAGE`).
- **Data Governance & Integrity:** Implement a 5-dimension Data Quality Assessment (DQA) and independently audit DAX calculations using SQL.

---

## 🎨 Dashboard Design & Aesthetics

The dashboard adheres to a custom visual design architecture engineered for cognitive clarity:

- **Operational Domain (Deep Teal - `#005F73`):** Applied to volume metrics, patient counts, line trends, and care-setting distributions.
- **Financial Domain (Maroon - `#800020`):** Applied to claim costs, coverage payouts, and payer pie/donut share distributions.
- **Layout Architecture:** White background (`#FFFFFF`) with 8px rounded corners, structured top header navigation, transparent metric icons, and dynamic slicers (`Year`, `Care Setting / Class`, `Payer`).

---

## 📊 Core Key Performance Indicators (KPIs)

| Metric | Category | DAX Formula Summary | Business Significance |
| :--- | :--- | :--- | :--- |
| **Total Encounters** | Operational | `COUNTROWS('Encounters')` | Measures overall patient encounter volume (`27.9K`). |
| **Total Unique Patients** | Operational | `DISTINCTCOUNT('Encounters'[PATIENT_ID])` | Evaluates actual clinical patient reach (`974`). |
| **Avg Total Claim Cost** | Financial | `AVERAGE('Encounters'[TOTAL_CLAIM_COST])` | Tracks mean gross billed charges per encounter (`$3.64K`). |
| **Avg Payer Coverage %** | Financial | `DIVIDE(SUM(PAYER_COVERAGE), SUM(TOTAL_CLAIM_COST), 0)` | Calculates net third-party reimbursement ratio (`30.63%`). |
| **Avg Encounter Duration** | Operational | *Custom DAX Timestamp Difference (DATEDIFF)* | Formats average visit duration into readable text (`7h 15m`). |

---

## 🧮 Key DAX Calculations

### Average Encounter Duration (Formatted String)
```dax
Average Encounter Duration =
VAR AvgMinutes =
    AVERAGEX(
        'Encounters',
        DATEDIFF('Encounters'[START], 'Encounters'[STOP], MINUTE)
    )
VAR TotalHours = INT(AvgMinutes / 60)
VAR TotalDays = INT(TotalHours / 24)
VAR RemainingHours = MOD(TotalHours, 24)
VAR RemainingMinutes = INT(MOD(AvgMinutes, 60))
RETURN
    IF(TotalDays > 0, TotalDays & "d ", "") &
    IF(RemainingHours > 0, RemainingHours & "h ", "") &
    RemainingMinutes & "m"
```

---

## 🔍 Data Governance & SQL Validation

To ensure executive confidence, all Power BI metrics were independently validated against raw source tables using SQL reconciliation scripts.

### Core Data Quality Checks:
1. **Completeness:** Verified 0% null values across mandatory primary and foreign keys (`ENCOUNTER_ID`, `PATIENT_ID`, `PAYER_ID`).
2. **Accuracy & Date Logic:** Audited timestamp logic to guarantee discharge time strictly follows admission (`STOP >= START`).
3. **Referential Integrity:** Confirmed 0 orphan records between historical encounter claims and the Payer master table.

### Sample SQL Validation Query (Payer Reimbursement Ratio):
```sql
SELECT 
    SUM(payer_coverage) AS total_payer_coverage_amount,
    SUM(total_claim_cost) AS total_claim_cost_amount,
    ROUND(
        (SUM(payer_coverage) / NULLIF(SUM(total_claim_cost), 0)) * 100, 
        2
    ) AS payer_coverage_percentage
FROM 
    encounters;
-- Verified SQL Result: 30.63% (100% Match with Power BI DAX Output)
```

---

## 🚀 Key Business Recommendations

1. **Shift Low-Acuity Volume:** Expand outpatient pathways to divert non-emergent visits away from high-cost hospital settings, optimizing emergency capacity.
2. **Renegotiate Payer Fee Schedules:** Address the ~69.37% uncollected claim variance by targeting primary commercial payers with below-average reimbursement rates.
3. **Seasonal Staffing Adjustments:** Align clinical staffing models with observed Q1 volume peaks (~3,023 encounters in Feb) to curb overtime expenses during operational lulls.

---

## 📁 Repository Structure

```text
├── dashboard/
│   └── Executive_Healthcare_Dashboard.pbix      # Power BI File
├── documentation/
│   ├── Data_Quality_Assessment.pdf              # DQA Framework Report (PDF)
│   ├── KPI_Definition_Sheet.docx                # Technical KPI Dictionary (Word)
│   ├── Validation_Evidence_Report.docx          # Task 4 Validation Report (Word)
│   └── Executive_Summary_Report.pdf             # Deliverable 6 Executive Summary (PDF)
├── sql/
│   └── sql_validation_evidence.sql              # Database Audit Scripts
└── README.md                                    # Project Overview
```

---

## 🛠️ How to View & Use

1. **Clone the Repository:**
   ```bash
   git clone https://github.com/your-username/healthcare-executive-dashboard.git
   ```
2. **Open Dashboard:** Download `Executive_Healthcare_Dashboard.pbix` and open it in **Power BI Desktop**.
3. **Review Documentation:** Navigate to the `/documentation` directory to inspect technical specifications, SQL audit scripts, and executive reports.