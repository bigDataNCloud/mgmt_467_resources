# Assignment 2 — Team Brief (Classification-Only, BQML)

## Flights (BigQuery) — Minimizing Diversion Disruption

### 1) Overarching business question
How should the airline set an operating policy to **pre-stage resources** for potential diversions (crews, gates, buses, hotel blocks) **while avoiding costly false alarms**? Build and compare diversion-risk classifiers for two decision horizons—**pre-departure** and **day-of-operations**—then recommend a **threshold (or policy)** that **minimizes expected disruption cost** and scales to high traffic volumes.

### 2) Four logistic regressions (one per student)

**Model A — Pre-departure baseline (global).**  
- **Goal:** early signal with schedule-level features only.  
- **Features:** `carrier`, `route = origin-dest`, `distance`, `day_of_week`, `month/season`.  
- **Outputs:** `ML.EVALUATE` (AUC/log_loss), confusion @ 0.5, **2–3 lines on calibration**.  
- **Hand-off:** establishes the no-real-time baseline.

**Model B — Day-of-ops uplift (global + engineered).**  
- **Goal:** quantify value of near-departure signals.  
- **Features:** Model A + `dep_delay` + `dep_delay_bucket` (early/on_time/minor/moderate/major) + optional `hour_of_day`.  
- **Outputs:** compare AUC/log_loss vs Model A; confusion @ 0.5; **3–5 lines on uplift drivers**.  
- **Hand-off:** shows marginal value of real-time info.

**Model C — Localized model (hub/route segment).**  
- **Goal:** test whether specialization improves calibration.  
- **Scope:** train/eval on a segment (e.g., origin in {ATL, ORD, JFK} or top N routes).  
- **Features:** same as Model B.  
- **Outputs:** AUC & **calibration vs Model B on the same segment**; confusion @ 0.5.  
- **Hand-off:** informs global vs segmented deployment.

**Model D — Threshold & cost policy.**  
- **Goal:** choose an operating threshold that minimizes expected cost.  
- **Inputs:** scores from the best global/segment model; **team-defined cost matrix** (e.g., `C_FP = $1,000`, `C_FN = $6,000`).  
- **Outputs:** confusion at chosen threshold, **expected-cost vs default (0.5)**, **3–5 sentence ops justification** + what to **monitor post-deploy**.

---

## Titanic (BigQuery) — Resource-Aware, Fair Triage Planning

### 1) Overarching business question
How should a maritime operator **prioritize limited staff attention** during an emergency to **maximize survival odds** **without systemic unfairness across subgroups** (e.g., class/sex)? Build a survival-likelihood classifier using **manifest-only** vs **engineered** features, consider **subgroup specialization**, and recommend a **threshold (or top-K policy) with fairness checks**.

### 2) Four logistic regressions (one per student)

**Model A — Pre-embarkation baseline (global).**  
- **Goal:** compact, interpretable manifest-only model.  
- **Features:** `pclass`, `sex`, `age`, `fare`, `embarked`.  
- **Outputs:** `ML.EVALUATE` (AUC/log_loss), confusion @ 0.5, **2–3 lines on interpretability**.  
- **Hand-off:** clean baseline before engineering.

**Model B — Engineered uplift (global + engineered).**  
- **Goal:** capture family/class interactions.  
- **Features:** Model A + `family_size = sibsp + parch + 1`, `fare_bucket` (low/mid/high), `sex_pclass` interaction.  
- **Outputs:** compare AUC/log_loss vs Model A; confusion @ 0.5; **3–5 lines on why it helped**.  
- **Hand-off:** shows benefit of simple features.

**Model C — Subgroup specialization (operational segment).**  
- **Goal:** test specialized policy where risk concentrates.  
- **Scope:** train/eval on one subgroup, e.g., `family_size > 1` **or** `pclass = 3` **or** `embarked = 'S'`.  
- **Features:** same as Model B.  
- **Outputs:** AUC & **calibration vs Model B on the same subgroup**; confusion @ 0.5.  
- **Hand-off:** informs global vs subgroup policy.

**Model D — Threshold & cost/fairness policy.**  
- **Goal:** pick operating threshold (or top-K) with **fairness visibility**.  
- **Inputs:** scores from Model B/C; **cost assumptions** (e.g., `C_FN = 4 × C_FP`) and at least one **fairness slice** (e.g., precision by `sex` or `pclass`).  
- **Outputs:** confusion at chosen threshold (or coverage at top-K), **expected cost vs 0.5**, **3–5 sentence policy note** including parity observation (**flag any gap > 5 pp**).

---

## Rubric (100 + up to 10 EC) — Classification-only

### Individual (60)
- Baseline or engineered model build + clear metrics (AUC/log_loss) — **15**  
- Confusion matrix interpretation (default 0.5) — **15**  
- Threshold rationale (0.75 or other; cost-aware or top-K) — **10**  
- TRANSFORM engineering + before/after comparison (where applicable) — **15**  
- Reproducibility (clear cells, params, no hidden magic) — **5**

### Team (40)
- Ops Brief clarity (exec-ready; risks & mitigations; decision rule) — **20**  
- Repository hygiene & repeatability (layout, SQLs, run steps) — **10**  
- Model governance notes (assumptions, limitations, monitoring plan) — **10**

### Extra Credit (up to +10)
- Additional model variant (e.g., different bucketing/interaction or a calibrated thresholding method) with rigorous comparison — **+5**  
- Sensitivity analysis (cost-based FP/FN optimization or threshold sweep with fairness bands) — **+5**

---

## Quick assignment tips (students)
- Keep each notebook focused: **one model per student**, 1–2 clean figures/tables, short prose.  
- Make your **assumptions explicit** (costs, capacity cap, segment choice).  
- Post-deployment: commit to **what you will monitor** and **who owns it**.

### Team Ops Brief (2–3 pages, PDF)
- **Decision rule (one line):** what you are optimizing (risk vs speed/capacity) and the **operating threshold** you chose.  
- **Evidence:** baseline → engineered → segment results; show key metrics (AUC, log_loss, confusion).  
- **Policy:** global vs segmented deployment; **expected-cost analysis** (your cost matrix); **fairness observation** (Titanic) or **hub precision** (Flights).  
- **Monitoring:** metric + threshold + cadence + owner (e.g., weekly calibration error; precision on top hubs; parity gaps < 5 pp).

---

# Recommended Repo Structure


Unit2_<Dataset>_TeamX/
├── individual/
│   ├── Unit2_Alice_BQML.ipynb      # one model per student (A/B/C/D)
│   ├── Unit2_Bob_BQML.ipynb
│   ├── Unit2_Charlie_BQML.ipynb
│   └── Unit2_Dana_BQML.ipynb
├── team/
│   ├── Ops_Brief.pdf               # 2–3 pages: policy, threshold, risks, monitoring
│   └── README.md                   # how to run, dataset choice, assumptions








