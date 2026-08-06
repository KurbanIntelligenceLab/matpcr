# MatPCR — code repository

This folder is the upload target for the manuscript's **Code availability**
statement (`version3/matpcr_snjnl.tex`): "All code, including the oracle
implementations with unit tests, the chain-parsing code, the metric code,
the DFT protocol settings, the prompts, and the released verifier weights."

## Contents

- `matpcr_metrics.py` — the oracle implementations (Bragg/XRD, SEM
  scale-bar, spectral peak, stability, metallicity, magnetism), the PCR
  metric, bootstrap CI, McNemar, ROC AUC, Cohen's kappa, Holm/BH
  correction, and a synthetic smoke test at the bottom of the file (run
  `python3 matpcr_metrics.py` to verify the estimators recover planted
  values before trusting anything else here).
- `experiments/*.py` — every experiment script referenced in the
  manuscript: item builders and prompts (`pilot.py`, `pilot_dft.py`), the
  Half-A/Half-B harnesses, the PCR sweep (`exp2_pcr_sweep.py`), the
  accuracy-vs-consistency analysis (`exp3_*.py`), CGSV (`exp4_cgsv.py`,
  `exp7_cgsv_controls.py`), the verifier training/eval pipeline
  (`exp5_*.py`, `exp6_*.py`), and the later robustness/audit scripts
  (`exp8`–`exp18`, including this release's `exp18_prompt_sensitivity.py`).
- `experiments/slurm/` — the Panther A100/H200 Slurm job scripts and setup
  notes (`README.md`, `RUNBOOK.md`) used for the later-stage re-runs.
- `verifier_weights/` — the released, seed-pinned LoRA adapters for all 13
  verifier training runs (in-distribution + 6 held-out-modality + 6
  held-out-constraint-type splits), trained on `Qwen2.5-0.5B-Instruct`.
- `matpcr_runbook.txt` — the experiment protocol in run order (gates,
  flag-back triggers, submission checklist) that this code implements.

## What is NOT here

Raw third-party datasets (opXRD, NFFA-EUROPE, RRUFF, Materials Project) are
NOT bundled — they are public data with their own citable sources (see the
manuscript's Data availability section and references). This folder is code
only. The MatPCR-specific dataset this code produced (model call records,
oracle labels, provenance) is in the separate `release_dataset/` folder.

## Reproducing a result

Each `experiments/exp*.py` script's module docstring states its exact
run command, inputs, and which manuscript table/figure it fills. Data paths
are read from the `MATPCR_DATA_ROOT` environment variable — set this to
wherever the raw datasets are staged locally before running anything that
touches real data (see `experiments/slurm/README.md` for the staging
scripts used on Panther).
