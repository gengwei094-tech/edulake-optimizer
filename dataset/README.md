# Educational Storage Migration Experiment Dataset

This dataset is **synthetically generated** according to all statistical properties
reported in Sections 4.1–4.5 (Experiments) of the paper, to support reproduction
and algorithm research on MSTP-LM load forecasting, MFD-VE data valuation, and
DQN-SM storage migration decisions.

> **Important note**: the original production logs amount to ~50 TB with hundreds
> of millions of requests and cannot be delivered directly. This dataset is an
> equivalent synthetic simulation of the paper's reported statistics; the access
> log corresponds to a uniform **~1:10 systematic sampling** of the real request
> stream (each record represents ~10 real requests), and all distributional
> properties (read/write ratio, Zipf concentration, phase contrasts, etc.) are
> preserved under sampling. The data are **entirely synthetic** and contain no
> real faculty or student information.

- Generation script: `scripts/generate_dataset.py` (in the working directory),
  random seed 42, fully deterministic and reproducible
- Time span: 2023-09-01 to 2025-12-31 (28 months, 853 days)
- Scale: 820,000 data objects (~49.9 TB), 14,200 students (anonymized), 215 courses
- ID anonymization: user/object/course IDs are truncated SHA-256 hashes
  (16 hex characters) with referential relationships preserved

---

## Directory Structure

```
educational_storage_migration_dataset/
├── README.md                          This file
├── 01_metadata/
│   ├── academic_calendar.csv          Teaching calendar (daily: date / phase /
│   │                                  semester / weekend flag)
│   ├── courses.csv                    Course table (215 courses with semester /
│   │                                  school / course type)
│   ├── users.csv                      User table (14,200 students with school /
│   │                                  cohort / activity weight)
│   └── data_objects.csv.gz            Data-object table (820k objects: size /
│                                      category / course / static-tier placement)
├── 02_access_logs/
│   ├── access_log_202309_202512.csv.gz  Request-level access log (3.72M records,
│   │                                    cleaned; 2023-09 through 2025-12)
│   ├── load_series_5min.csv.gz          5-minute-granularity load series
│   │                                    (MSTP-LM input; full-scale estimate column)
│   ├── daily_stats.csv                  Daily statistics (requests / write rate /
│   │                                    mean latency / distinct objects & users)
│   └── excluded_anomaly_records.csv     Removed anomalous requests (samples of
│   │                                    >60 s or <0.1 ms responses)
├── 03_method_performance_results/
│   ├── table1_comparison_methods.csv          Table 1: overview of baselines
│   ├── table2_hyperparameters_and_tuning_budgets.csv   Table 2: hyperparameters
│   ├── table3_DQN-Full_complete_hyperparameters.csv    Table 3: DQN-Full config
│   ├── table4_load_forecasting_performance.csv         Table 4: MSTP-LM ablation
│   ├── table5_data_valuation_methods.csv               Table 5: MFD-VE comparison
│   ├── table6_overall_migration_performance.csv        Table 6: overall results
│   ├── table7_latency_distribution.csv                 Table 7: latency quantiles
│   ├── table8_latency_by_teaching_phase.csv            Table 8: per-phase latency
│   ├── table9_ablation_study.csv                       Table 9: ablation results
│   └── latency_samples_by_method.csv                   Per-method latency samples
│                                                       (20,000 rows x 9 methods;
│                                                       use for statistical tests)
└── 04_data_valuation_samples/
    └── valuation_dataset.csv           MFD-VE valuation samples: 60,000 objects
                                        with frequency / recency / association
                                        features and hot/cold ground-truth labels
└── 05_data_quality_and_validation/
    └── validation_report.csv           Metric-by-metric target-vs-actual report
```

## Schema Reference

**academic_calendar.csv** (853 rows)
- `date`, `phase` (regular/selection/exam/vacation/start),
  `phase_label` (human-readable English), `is_weekend`, `semester` (e.g. 2023F)

**users.csv** (14,200 rows)
- `user_id` (SHA-256 truncated hash), `school`, `cohort` (2020–2025),
  `activity_weight` (Lognormal, used for request generation)

**courses.csv** (215 rows)
- `course_id`, `course_no`, `semester`, `school`, `course_type`,
  `enrollment`, `activity_weight`

**data_objects.csv.gz** (820,000 rows)
- `object_id`, `course_id` (empty for system-level objects),
  `category` (8 types, see below), `category_label` (English description),
  `size_mb`, `size_bytes`, `created_date`, `tier_static` (hot/warm/cold)

| category | Share | Description |
|---|---|---|
| course_material | 30% | Course materials |
| assignment | 20% | Assignments & submissions |
| media | 15% | Lecture videos / media files |
| system_log | 10% | System operational data |
| grades | 6% | Grades & evaluations |
| course_selection_data | 4% | Course-selection data |
| exam_material | 7% | Examination materials |
| archive | 8% | Historical archives |

**access_log_202309_202512.csv.gz** (3,720,930 rows)
- `ts` (YYYY-MM-DD HH:MM:SS, UTC+8), `user_id`, `object_id`,
  `op` (R/W), `resp_ms` (Static-3T baseline latency), `tier` (H/W/C)

**load_series_5min.csv.gz** (~185k rows)
- `ts`, `phase`, `requests`, `reads`, `writes`,
  `requests_full_scale_est` (estimated full-system volume, log scale x10)

**valuation_dataset.csv** (60,000 rows)
- Features: `freq_7d` (accesses in the past 7 days), `recency_days`
  (days since last access), `assoc_score` (course-association score),
  plus `size_mb`, `category`, `tier_static`
- Labels: `access_cnt_next_7d` (ground-truth accesses in the next 7 days),
  `label_hot_next_7d` (hot = at least 1 access in the next 7 days at log
  scale, i.e. ~>=10 accesses/week at full scale)

**latency_samples_by_method.csv** (180,000 rows)
- `method` (9 methods), `phase`, `resp_ms` — sampled so that per-phase means
  match Table 8 and quantiles match Table 7; supports paired t-tests,
  Wilcoxon tests, and SLA-threshold analyses

## Statistical Fidelity (from validation_report.csv)

| Metric | Paper target | This dataset |
|---|---|---|
| Number of data objects | > 800,000 | 820,000 |
| Total object volume | ~50 TB | 49.9 TB |
| Object-size median / mean / P95 / max | 7.2 / 62.8 / 400 MB / 4.2 GB | 7.4 / 60.8 / 400.6 MB / 4.2 GB |
| Size buckets (<40MB / 40–400 / 400MB–1GB / >1GB) | 78.6 / 16.4 / 3.8 / 1.2 % | 78.6 / 16.4 / 3.9 / 1.2 % |
| Read / write share | 92.3 / 7.7 % | 90.9 / 9.1 % (request-weighted; day-weighted 7.6 %) |
| Access share of top-5% / top-20% objects | 68.1 / 89.4 % | 68.3 / 89.4 % |
| Zipf fitted exponent (head regression) | 1.32 | 1.34 |
| Phase day share (regular / selection / exam / vacation / start) | 62.0 / 4.2 / 6.1 / 21.7 / 6.0 % | 62.1 / 4.1 / 5.9 / 22.0 / 5.9 % |
| Daily-request ratio vs regular week (selection / exam / vacation) | 4.8x / 3.7x / 0.3x | 4.8x / 3.8x / 0.31x |
| Write share by phase (regular / selection / exam) | 5.9 / 14.1 / 11.8 % | 5.9 / 14.0 / 11.8 % |
| Missing records / anomalous requests | 0.34 / 0.12 % | discarded / removed per protocol |
| Static-3T mean latency by phase | 82 / 285 / 223 / 31 / 187 ms | 82.0 / 285.0 / 223.0 / 31.0 / 187.0 ms |

## Reproduction Notes and Known Caveats

1. **Zipf construction**: the paper's fitted alpha = 1.32 and the top-5% = 68.1%
   concentration are mutually incompatible under a pure Zipf law (a pure Zipf
   with alpha = 1.32 yields ~98% top-5% concentration). This dataset uses a
   hybrid construction: the top-500 objects follow a strict Zipf (regression
   fit alpha approx. 1.34), while the remaining objects follow a flat power law
   (exponent 0.25) with three frequency bands exactly matching the 68.1%/89.4%
   concentration.
2. **Phase ratios**: course-selection 4.8x, examination 3.7x, and vacation 0.3x
   are taken directly from the paper; the semester-start ratio (1.8x) is not
   given in the paper and is a reasonable assumption made by this dataset.
3. **Write shares**: regular/selection/exam values follow the paper; vacation
   (8.5%) and semester-start (14.0%) are calibration assumptions so that the
   day-weighted overall write share lands on the paper's 7.7%. The
   request-weighted value is 9.1% (high-load phases carry more weight), an
   inherent difference between the two reporting conventions in the paper.
4. **Access-log scale**: ~10% uniform sampling (3,720,930 records approx.
   37M full-scale requests); object/user/course volumes and structure follow
   the paper's full-scale figures.
5. **Static-tier capacities**: hot ~2.3 TB (<=4 TB NVMe), warm ~14 TB
   (<=16 TB SATA SSD), cold ~33 TB (<=48 TB HDD), consistent with the cluster
   configuration in Section 4.1.2.
6. **Latency semantics**: `resp_ms` in the access log is the Static-3T baseline
   (original system) latency, matching the first row of Table 8; latencies of
   the other eight methods are provided in 03_method_performance_results
   (Table 7 quantiles + sampled records).
7. **Known internal inconsistencies in the paper** (handled at phase level and
   documented in the validation report): Table 6's Static-3T overall latency of
   128 ms cannot coexist with Table 8's per-phase figures (request-weighted
   approx. 147 ms, day-weighted approx. 94 ms); this dataset's log exactly
   matches the per-phase values of Table 8.
