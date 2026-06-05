# Experi

Free A/B experiment design tool, now rebuilt on Databricks.

Live demo: https://theshlok13-experi.hf.space

Original frontend: https://tryexperi.netlify.app

## What it does

- Sample size and runtime calculator
- CUPED variance reduction (saves 30-40% of users)
- Bayesian inference: P(Treatment > Control) instead of p-values
- Posterior distribution visualization
- Full experiment logging with MLflow: Every run tracked, versioned, auditable
- Results persisted to Delta Lake with full lineage

## What changed in v2

The original version ran entirely in the browser. Experiment results lived in browser state with no lineage, no governance, and no way to reproduce an analysis six months later. The Databricks rebuild fixes that. Every experiment is logged to MLflow, results are stored in Delta Lake, and the full pipeline is reproducible from raw events.

## Files

- `experi_pipeline.ipynb` — Databricks pipeline with Delta Lake, MLflow, and Bayesian inference
- `index.html` — original browser-based frontend (v1)

## Stack

v1: Vanilla HTML, CSS, JavaScript. No dependencies. Single file.

v2: Databricks, Delta Lake, MLflow 3.0, Gradio, Python

## Background

Built from patterns observed running experiments across startups and research at Purdue University. Originally developed after working as a Forward Deployed Engineer at VortexifyAI (YC F24).

By Shlok Sheth — https://shethshlok.netlify.app
