# IRB Credit Risk Modeling – Synthetic PD & LGD Datasets

This repository contains synthetic datasets and notebooks designed to simulate key concepts in IRB credit risk modeling, with a focus on:

- PD (Probability of Default)  
- LGD (Loss Given Default)  

Both are central components in regulatory and internal risk quantification frameworks.

## 1. PD Modeling

The PD dataset (`pd_dataset_easy.csv`) is snapshot-based, with one row per customer per year.  
It is structured to support standard IRB-level PD model development and validation:

- Feature engineering on customer-level attributes  
- Default flag creation  
- Statistical validation: monotonicity, binning, KS tests, and more  

See: `IRB_PD.ipynb`

## 2. LGD Dataset: Event-Based Simulation

The LGD dataset (`lgd_dataset.csv`) extends the PD concept with a more complex, event-based structure to reflect how LGD is modeled in production systems.

Each customer may have multiple rows, representing key credit lifecycle events:

- `event_type = "default"` – the year default occurs  
- `event_type = "recovery"` – the year recovery is recorded  

This design reflects real-world LGD behavior, where recovery may occur one or more years after the initial default.

## Added Columns to LGD 

| Column             | Description                                                                 |
|--------------------|-----------------------------------------------------------------------------|
| `event_type`        | "default" or "recovery" per customer and year                              |
| `recovered_amount`  | Amount recovered after default                                              |
| `recovery_rate`     | Share of exposure recovered = `recovered_amount / exposure_at_default`     |
| `lgd`               | Loss Given Default = `1 − recovery_rate`                                   |
| `batch_run_date`    | Timestamp of batch generation; used to filter out outdated duplicate rows  |
| `segment`           | Customer segment (e.g., "Corporate" or "Retail") added for LGD modeling to enable segmentation and enhanced analysis |

## Data Complexity and Preprocessing

This LGD dataset includes realistic data issues commonly encountered in IRB modeling:

- Duplicate recovery entries for a customer  
- Rows where `recovered_amount > exposure_at_default`, resulting in `lgd < 0`  
- One row with missing recovery data  
- Varying `batch_run_date` values across duplicate rows  

To simulate these conditions, the data must be sorted by `batch_run_date` and filtered to retain only the most recent entry per customer.  
This mimics real-world LGD pipelines, where data often arrives in batches and may be revised over time.

<font color="red">
  
## LGD vs. PD – Modeling Focus

| Aspect               | PD Modeling                             | LGD Modeling                                         |
|----------------------|------------------------------------------|------------------------------------------------------|
| Structure            | Snapshot-based (one row per year)        | Event-based (multiple rows per customer)             |
| Validation focus     | Statistical tests (KS, PSI, etc.)        | Data integrity and lifecycle handling                |
| Common challenges    | Imbalanced data                          | Delayed recoveries, duplicates, missing values       |
| Regulatory alignment | Basel-compliant scoring                  | Lifecycle LGD estimation and downturn adjustments    |

<p><b>Note:</b> Unlike LGD, PD modeling typically does not segment by customer type or other segments due to the large sample sizes and the focus on probability estimation. In contrast, LGD modeling often requires segmentation and more advanced data preprocessing (such as handling duplicates and batch filtering) because the dataset is smaller, event-based, and more complex. This ensures model stability and regulatory compliance without overwhelming the reader with excessive testing detail.</p>

</font>

## Purpose of this Project

This repository demonstrates not only the technical implementation of IRB-aligned modeling logic, but also:

- How PD and LGD differ structurally  
- Why LGD often requires stricter data hygiene  
- The role of fields like `batch_run_date` in production data pipelines  
- How synthetic data can be designed to simulate real-world risk analytics workflows  

This is a sandbox project created for demonstration and job discussion purposes only.  
No real customer data is used. All datasets are fully synthetic.
