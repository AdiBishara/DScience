# Critical Reproduction: Autoencoder-Based Network Intrusion Detection on CIC-IDS2017

**Final Project — Data Science in Cyber (Dr. Uri Itai), University of Haifa**
**Author:** Adi Bishara

A critical reproduction and evaluation of **brett-gt's *Intrusion Detection System***,
an autoencoder-based network anomaly detector trained on the CIC-IDS2017 dataset.
This project reproduces the approach, audits its methodology, and re-evaluates it
under a leakage-free, prevalence-aware protocol — and adds a second model
(Isolation Forest) as a baseline.

## Source under review
- **Project:** *Intrusion Detection System* (GitHub user *brett-gt*) —
  https://github.com/brett-gt/IntrusionDetectionSystem
- **Dataset:** CIC-IDS2017 (Canadian Institute for Cybersecurity) —
  https://www.unb.ca/cic/datasets/ids-2017.html
- **Supporting reference (dataset):** Sharafaldin, Lashkari & Ghorbani,
  *Toward Generating a New Intrusion Detection Dataset and Intrusion Traffic
  Characterization*, ICISSP 2018.

## Headline findings

| Aspect | Source claim | Our leakage-free result |
|---|---|---|
| Attack detection (TPR) | 98.3% | recall 0.736 |
| False-positive rate | 8.7% | 0.040 |
| ROC-AUC | not reported | **0.895** |
| MCC | not reported | **0.738** |
| Precision @ 1% prevalence | not reported | **≈ 0.16** |

**Model comparison (test set):**

| Metric | Autoencoder | Isolation Forest |
|---|---|---|
| ROC-AUC | 0.895 | 0.868 |
| Precision | 0.894 | 0.819 |
| Recall | 0.736 | 0.734 |
| F1 | 0.807 | 0.774 |
| MCC | 0.738 | 0.681 |

**Conclusion.** The autoencoder approach is genuinely discriminative, but the
source's headline numbers are inflated by (1) ~23% exact-duplicate records that
leak across splits, (2) a decision threshold tuned on the test set, and (3)
prevalence-blind metrics. At realistic attack rates, precision collapses. Full
analysis is in [`report/report.pdf`](report/).

## Repository structure
```
.
├── README.md
├── requirements.txt
├── report/
│   ├── report.docx                       # full written report (source)
│   └── report.pdf                        # full written report (submission copy)
├── notebook/
│   └── DS_Cybersecurity_project.ipynb     # complete, executed analysis notebook
├── data/                                  # CIC-IDS2017 CSVs (NOT committed; see below)
└── results/
    ├── figures/                           # generated figures
    └── tables/                            # generated result tables
```

## Dataset source
CIC-IDS2017 "MachineLearningCSV" (MachineLearningCVE) flow CSVs (8 files,
Monday–Friday). Download from the Canadian Institute for Cybersecurity and place
the CSVs under:
```
data/raw/MachineLearningCSV/MachineLearningCVE/
```

## Setup
Python 3.10+ recommended.
```bash
python -m venv venv
venv\Scripts\activate           # Windows  (use: source venv/bin/activate on Linux/Mac)
pip install -r requirements.txt
```

## How to run
The notebook runs top-to-bottom on CPU or GPU.
```bash
jupyter notebook notebook/DS_Cybersecurity_project.ipynb
# Run all cells (Kernel -> Restart & Run All)
```
The autoencoder trains on benign-only traffic; the decision threshold is selected
on a held-out validation split and reported on a disjoint test split. Exact-duplicate
flow records are removed before splitting to prevent train/test leakage.

## Reproducibility notes
- All randomness uses a single fixed seed (`RANDOM_STATE = 42`).
- First run processes the raw CSVs and caches features to `data/processed/`;
  delete that folder to force a clean recomputation.
- Reported metrics are computed once on the held-out test set after threshold
  selection on validation (no threshold leakage).
