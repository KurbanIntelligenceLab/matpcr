# MatPCR — code repository

## Abstract

Vision-language and language models increasingly interpret materials data, yet benchmarks report that they hallucinate invalid properties and violate physical law. Evaluation matches final answers to scarce human labels, while discovery agents verify final proposals or density functional theory (DFT) execution. Neither measures the physical consistency of a model's reasoning chain. Materials data carries its own physics, making a large class of materials reasoning verifiable without annotation. We introduce MatPCR, a label-free benchmark whose programmatic oracles check diffraction geometry through Bragg's law, scale bars, spectral peaks, and DFT-grounded stability, metallicity and property magnitude. We define the Physical-Consistency Rate over image and structure inputs; introduce Constraint-Grounded Self-Verification, an agentic loop whose gain survives self-refinement and equal-compute re-prompting controls; release an open verifier useful in distribution but near chance on all six held-out constraint types; and derive an exact identity for how oracle error displaces the reported rate.

## Contents

- `matpcr_metrics.py` — the oracle implementations (Bragg/XRD, SEM scale bar, spectral peak,
  stability, metallicity, magnetism), the PCR metric, bootstrap CI, exact McNemar, ROC AUC,
  Cohen's kappa, Holm/BH correction, and a synthetic smoke test at the bottom of the file
  (`python3 matpcr_metrics.py` checks that the estimators recover planted values).
- `experiments/final_oracle.py` — applies the final stability rule (near-hull abstention band) to
  already-collected records. `MATPCR_STABILITY_RULE=original` reproduces the original rule
  reported in the Supplementary Information.
- `experiments/*.py` — every experiment and analysis script: item builders and prompts
  (`pilot.py`, `pilot_dft.py`), the Half-A/Half-B oracle-validity harnesses, the PCR sweep
  (`exp2_*`), accuracy versus consistency (`exp3_*`), CGSV and its controls (`exp4_*`, `exp7_*`),
  verifier training and evaluation (`exp5_*`, `exp6_*`, `exp8_*`), robustness and audit analyses
  (`exp9`–`exp18`), figures (`exp19`, `exp20`) and the analyses added to the revised manuscript
  (`exp21`–`exp41`). Each script's docstring gives its run command, inputs and the table or figure
  it produces.
- `matpcr_runbook.txt` — the experiment protocol in run order.
- `requirements-lock.txt` — exact package versions of the environment that produced the reported
  numbers (Python 3.10).

## Data and weights

- MatPCR items, model responses, oracle labels and reference values with provenance:
  Zenodo, https://doi.org/10.5281/zenodo.21829643 (always resolves to the latest version).
- The 13 verifier adapters (LoRA on `Qwen2.5-0.5B-Instruct`):
  https://huggingface.co/rasulkhanbayov/matpcr-verifier
- Raw third-party data (opXRD, NFFA-EUROPE, RRUFF, Materials Project) is not redistributed here;
  see the manuscript's Data availability section for the sources.

## Reproducing the reported numbers

**Environment.** `pip install -r requirements-lock.txt`. `pytesseract` also needs the `tesseract`
binary (for example `conda install -c conda-forge tesseract`).

**Placement.** Download the Zenodo record, then:

- put every `*.json`, `*.jsonl` and review `*.txt` file into `experiments/`
  (the four `*_halfB_review_2026-08-06.txt`, `xrd.txt` and `sem.txt` go in the repository root);
- put `mp_reference_batch.jsonl`, `mp_polymorphs.json` and `mp_provenance.json` into
  `$MATPCR_DATA_ROOT/mp_data/`;
- unzip `human_review_R1.zip` into `KBS_revision_R1/` (giving `KBS_revision_R1/human_review/...`);
- unzip `analysis_logs_R1.zip` into `experiments/`.

Raw opXRD and RRUFF files go under `$MATPCR_DATA_ROOT/opxrd/` and `$MATPCR_DATA_ROOT/rruff/`.
Set `MATPCR_DATA_ROOT` before running anything that reads raw data.

**Analyses without API cost.** `exp21`–`exp28`, `exp34`, `exp36`–`exp38`, `exp40`, `exp41`, `exp9`
and `exp31_si_tables.py --out <file>` regenerate the corresponding tables from the files above.
`exp28` needs `--query` to recompute its second part (otherwise it reads the cached
`mp_polymorphs.json`). `exp29` and `exp30` make paid API calls; their outputs are in the Zenodo
record. Scripts that query models need an OpenRouter key (`OPENROUTER_API_KEY`), and
`mp_fetch_reference_data.py` / `exp28 --query` need a Materials Project key (`MP_API_KEY`).
