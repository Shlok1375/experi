# Experi

A/B test analysis with CUPED variance reduction and Bayesian inference, built as a reproducible pipeline on Databricks.

- Pipeline (v3): [experi_pipeline_v3.ipynb](experi_pipeline_v3.ipynb)
- Live demo: https://theshlok13-experi.hf.space/ (runs the same analysis in the browser, no Databricks account needed)
- Original tool (v1): https://tryexperi.netlify.app

## How the pipeline works

1. Raw product events (visits, purchases) are written to a Delta table in Unity Catalog.
2. Pre-experiment data is read from the table version that existed at launch, using Delta **time travel**. Data that arrives later can't change the result.
3. Per-user covariates and outcomes are computed in Spark.
4. CUPED removes the part of each user's outcome that their past behavior already predicted.
5. Bayesian inference estimates the probability that treatment beats control.
6. Parameters, table versions, and metrics are logged to MLflow.
7. Results are written to Unity Catalog, with lineage from raw events to the final decision.

## Results

Simulated data, 20,000 users.

| Check | Result |
|---|---|
| Pre-period purchases: launch version vs. latest | 10,724 vs. 11,046 |
| CUPED variance reduction | 5.5% |
| P(treatment > control) | above 0.999 |
| Lift | +2.4 points (12% relative) |
| Decision | Ship |

The gap between 10,724 and 11,046 is a late backfill. Reading the launch version keeps the analysis reproducible.

The variance reduction is modest because past purchases only partly predict conversion (correlation 0.235, and 0.235² ≈ 5.5%). CUPED helps most when past behavior strongly predicts the outcome.

## Versions

- v1: Browser tool in HTML/CSS/JS. Correct statistics, but results lived in browser state and couldn't be reproduced.
- v2: First Databricks version ([experi_pipeline.ipynb](experi_pipeline.ipynb)). Added MLflow logging and Delta output, using simulated pandas data.
- v3: Raw events in Delta, time-travel reads, Spark, and Unity Catalog lineage. Also fixes the v2 inference: CUPED-adjusted values aren't 0/1, so they're now modeled with a Normal posterior instead of a Beta.

## Run it

Import `experi_pipeline_v3.ipynb` into Databricks (Free Edition works), attach Serverless compute, and click Run all. If your catalog isn't named `workspace`, change `CATALOG` in the first code cell.

## Stack

Databricks, Delta Lake, Unity Catalog, MLflow 3, PySpark, Python. The demo uses Gradio on Hugging Face Spaces.

## Background

Built from patterns I saw running experiments at startups and in research at Purdue University. Started after working as a Forward Deployed Engineer at VortexifyAI (YC F24).

Shlok Sheth, https://shethshlok.netlify.app
