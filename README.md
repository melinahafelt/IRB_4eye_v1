# IRB Credit Risk Modeling – Synthetic PD and LGD Datasets

This repository provides synthetic datasets and notebooks designed to simulate key components of credit risk modeling under the Internal Ratings-Based (IRB) approach, with a primary focus on:

- Probability of Default (PD)  
- Loss Given Default (LGD)

Both are foundational inputs to regulatory capital calculations and internal risk management under Basel II/III and CRR frameworks.

## 1. Probability of Default (PD) Modeling

The `pd_dataset_easy.csv` file is a snapshot-based dataset containing one observation per customer per year. It is structured to support standard PD modeling pipelines:

- Feature engineering on customer-level attributes  
- Binary default flag creation  
- Statistical validation techniques including monotonicity analysis, binning, and Kolmogorov–Smirnov tests

Notebook: `IRB_PD.ipynb`

## 2. Loss Given Default (LGD) – Event-Based Simulation

The `lgd_dataset.csv` file extends the PD approach to a more complex, event-driven dataset that simulates real-world LGD modeling logic. It includes multiple observations per customer to reflect different stages of the credit lifecycle:

- `event_type = "default"`: Indicates the year of default  
- `event_type = "recovery"`: Represents recovery activity in subsequent years

This structure mimics actual LGD behavior, where recoveries may occur years after the initial default.

### Key Columns

| Column             | Description                                                                 |
|--------------------|-----------------------------------------------------------------------------|
| `event_type`       | "default" or "recovery" per customer and year                              |
| `recovered_amount` | Amount recovered following default                                          |
| `recovery_rate`    | Fraction of exposure recovered, calculated as `recovered_amount / exposure_at_default` |
| `lgd`              | Loss Given Default, calculated as `1 − recovery_rate`                      |
| `batch_run_date`   | Timestamp for batch control and duplicate filtering                        |
| `segment`          | Customer segment (for example, "Corporate" or "Retail") for segmentation logic |

## 3. Data Complexity and Preprocessing Requirements

The LGD dataset includes several realistic data imperfections commonly encountered in production environments:

- Duplicate recovery entries per customer  
- Cases where recovered amount exceeds exposure at default, resulting in negative LGD  
- Rows with missing recovery information  
- Variability in batch run date for the same customer

These conditions simulate typical data challenges in production LGD models. Cleaning steps may include:

- Sorting by batch run date and retaining the most recent entries  
- Filtering invalid recovery records  
- Aggregating or analyzing by segment

## PD vs. LGD: Structural and Modeling Differences

| Feature                | PD Modeling                               | LGD Modeling                                          |
|------------------------|--------------------------------------------|--------------------------------------------------------|
| Data Structure         | Snapshot-based (one row per year)         | Event-based (multiple events per customer)            |
| Focus of Validation    | Statistical validation such as calibration and distribution tests | Data lifecycle integrity, segmentation, duplicate handling |
| Common Challenges      | Class imbalance, rare events              | Delayed recoveries, duplicate rows, missing values     |
| Segmentation Needs     | Often uniform across population           | Typically requires segmentation by business type       |
| Regulatory Context     | Basel-compliant scoring models            | Lifecycle-based estimation with downturn adjustments   |

Note: LGD modeling generally involves smaller and more complex datasets than PD modeling. It often requires enhanced preprocessing logic and more careful segmentation. PD modeling typically benefits from large sample sizes and more straightforward binary classification techniques.

## 4. Purpose of the Project

This repository was created as a sandbox environment to demonstrate:

- End-to-end IRB-compliant PD and LGD modeling workflows  
- Structural differences between PD and LGD datasets  
- Data preparation techniques used in real-world LGD pipelines  
- How synthetic data can simulate production-grade modeling environments

## Disclaimer

This project is entirely synthetic and created solely for demonstration, education, and professional discussion purposes.  
No real customer data is used. All datasets are fully anonymized and artificial.
