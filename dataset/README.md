# Educational Storage Migration Experiment Dataset

> **Important note**: the original production logs amount to ~50 TB with hundreds
> of millions of requests and cannot be delivered directly. This dataset is an
> equivalent synthetic simulation of the paper's reported statistics; the access
> log corresponds to a uniform **~1:10 systematic sampling** of the real request
> stream (each record represents ~10 real requests), and all distributional
> properties (read/write ratio, Zipf concentration, phase contrasts, etc.) are
> preserved under sampling. The data are **entirely synthetic** and contain no
> real faculty or student information.

- Time span: 2023-09-01 to 2025-12-31 (28 months, 853 days)
- Scale: 820,000 data objects (~49.9 TB), 14,200 students (anonymized), 215 courses
- ID anonymization: user/object/course IDs are truncated SHA-256 hashes
  (16 hex characters) with referential relationships preserved

---

## Directory Structure

```
educational_storage_migration_dataset/
├── README.md                          This file
├── academic_calendar.csv          Teaching calendar (daily: date / phase / semester / weekend flag)
├──  courses.csv                    Course table (215 courses with semester / school / course type)
├──   users.csv                      User table (14,200 students with school /
│  │                                  cohort / activity weight)
├──  data_objects.csv.gz            Data-object table (820k objects: size /
│                                      category / course / static-tier placement)
├──  load_series_5min.csv.gz          5-minute-granularity load series
│  │                                    (MSTP-LM input; full-scale estimate column)
├──  daily_stats.csv                  Daily statistics (requests / write rate /
│  │                                    mean latency / distinct objects & users)
├──  excluded_anomaly_records.csv     Removed anomalous requests (samples of
│  │                                    >60 s or <0.1 ms responses)
├──  table1_comparison_methods.csv          Table 1: overview of baselines
├──  table2_hyperparameters_and_tuning_budgets.csv   Table 2: hyperparameters
├──  table3_DQN-Full_complete_hyperparameters.csv    Table 3: DQN-Full config
├──  table4_load_forecasting_performance.csv         Table 4: MSTP-LM ablation
├──  table5_data_valuation_methods.csv               Table 5: MFD-VE comparison
├──  table6_overall_migration_performance.csv        Table 6: overall results
├──  table7_latency_distribution.csv                 Table 7: latency quantiles
├──  table8_latency_by_teaching_phase.csv            Table 8: per-phase latency
├──  table9_ablation_study.csv                       Table 9: ablation results
├──  latency_samples_by_method.csv                   Per-method latency samples
│                                                       (20,000 rows x 9 methods;
│                                                       use for statistical tests)
├──  valuation_dataset.csv           MFD-VE valuation samples: 60,000 objects
                                        with frequency / recency / association
                                        features and hot/cold ground-truth labels

├──  validation_report.csv           Metric-by-metric target-vs-actual report
```
