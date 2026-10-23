# Experi

A/B experiment analysis with CUPED variance reduction and Bayesian inference, rebuilt as a governed pipeline on Databricks.

- **Pipeline (v3):** [`experi_pipeline_v3.ipynb`](experi_pipeline_v3.ipynb)
- **Live demo:** https://theshlok13-experi.hf.space/ — runs the same CUPED + Bayesian analysis interactively (no Databricks account needed)
- **Original frontend (v1):** https://tryexperi.netlify.app

## What the v3 pipeline does

1. Writes raw product events (visits, purchases) to a Unity Catalog Delta table
2. Reads pre-experiment covariates from the Delta version that existed at experiment launch (**time travel**), so late-arriving data can't change results
3. Computes per-user covariates and outcomes in **Spark**
4. Applies **CUPED** variance reduction
5. Runs **Bayesian inference**: Beta-Binomial on raw conversion, Normal posterior on the CUPED-adjusted outcome
6. Logs parameters, Delta versions, and metrics to **MLflow**
7. Writes results to Unity Catalog, with lineage `raw_events → user_metrics → experiment_results`

## Results (simulated data, 20,000 users)

| | |
|---|---|
| Pre-period purchases at launch snapshot vs. latest version | 10,724 vs. 11,046 (a late backfill; pinning keeps results reproducible) |
| CUPED variance reduction | 5.5% (matches theory: corr² = 5.5%) |
| P(treatment > control) | > 0.999 |
| Lift | +2.4 pts absolute (+12.0% relative) |
| Decision | SHIP |

CUPED's benefit depends on how well pre-experiment behavior predicts the outcome. Here the correlation is 0.235, so the reduction is modest.

## Versions

- **v1:** Browser-based tool (HTML/CSS/JS). Sound statistics, but results lived in browser state with no lineage or reproducibility.
- **v2:** First Databricks rebuild ([`experi_pipeline.ipynb`](experi_pipeline.ipynb)). Added MLflow logging and Delta output, on simulated pandas data.
- **v3:** Governed pipeline. Raw events in Delta, time-travel reads, Spark processing, Unity Catalog lineage. Also corrects v2's inference: CUPED-adjusted values aren't 0/1, so they're now modeled with a Normal posterior instead of a Beta.

## Stack

Databricks, Delta Lake, Unity Catalog, MLflow 3.0, PySpark, Python. Demo: Gradio on Hugging Face Spaces.

## Run it

Import `experi_pipeline_v3.ipynb` into Databricks (Free Edition works), attach Serverless compute, and click **Run all**. Change `CATALOG` in the config cell if yours isn't `workspace`.

## Background

Built from patterns observed running experiments across startups and research at Purdue University. Originally developed after working as a Forward Deployed Engineer at VortexifyAI (YC F24).

By Shlok Sheth — https://shethshlok.netlify.app
