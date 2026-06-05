# IEEE-CIS Fraud Detection

LightGBM-based fraud detection model on the [IEEE-CIS Fraud Detection](https://www.kaggle.com/competitions/ieee-fraud-detection) Kaggle competition (Vesta Corporation dataset, 590,540 training transactions with 394 transaction features and 41 identity features).

**Kaggle leaderboard results:**

- Private: **0.9170 AUC**
- Public: **0.9394 AUC**

## About this project

This notebook works through the IEEE-CIS competition with an emphasis on data analyst reasoning alongside modelling performance. Each modelling iteration closes with a short *Findings* markdown cell explaining what changed, what improved, and what trade-offs were accepted — including reporting of null results, methodological choices (e.g., choosing semantically correct categorical handling despite a small AUC regression), and a quantified selection-bias diagnostic at the end.

## Approach

1. **Exploratory data analysis** — fraud rate over time, distributional patterns by product code, transaction amount, and card network
2. **Baseline modelling** — LightGBM with minimal preprocessing; diagnosed and addressed the spurious top-feature ranking of `TransactionDT`
3. **Modelling iterations** — native categorical handling, engineered cyclic time features, frequency encoding for high-cardinality identifiers
4. **Hyperparameter tuning** — Optuna search over LightGBM's capacity and regularisation parameters
5. **Final model and submission** — full-data refit with the tuned configuration, plus an honest calibration discussion comparing local validation AUC (0.9342), local held-out test AUC (0.9038), and Kaggle private score (0.9170)

## Repository structure

```
.
├── detection_model.ipynb      # main notebook (EDA, modelling, findings, submission)
├── requirements.txt           # Python dependencies
├── .gitignore                 # excludes datasets/, venv/, submission CSVs
└── README.md                  # this file
```

## Running the notebook

**1. Clone the repo and set up the environment:**

```bash
git clone https://github.com/zainridzuan/ieee-fraud-detection.git
cd ieee-fraud-detection
python -m venv venv
source venv/bin/activate    # macOS/Linux
pip install -r requirements.txt
```

**2. Download the dataset.** The IEEE-CIS dataset is ~1.3GB and not bundled with this repo. Get it from the [Kaggle competition data page](https://www.kaggle.com/competitions/ieee-fraud-detection/data) (requires a Kaggle account) and place the CSVs in a `datasets/` directory at the project root:

```
datasets/
├── train_transaction.csv
├── train_identity.csv
├── test_transaction.csv
├── test_identity.csv
└── sample_submission.csv
```

**3. Open `detection_model.ipynb` and run all cells.** A full top-to-bottom run takes approximately 15–20 minutes on a modern laptop, dominated by the 50-trial Optuna hyperparameter search.

## Key sections to look at

If you only have a few minutes:

- **Opening cell** — problem framing and headline result
- **Findings cells** — each modelling iteration closes with a short *Findings* cell summarising what changed and why; these are the analytical core of the notebook
- **Final Kaggle findings** — closing discussion of the local validation → local test → Kaggle private calibration story and the roughly 3pp HPO selection-bias gap quantified directly

## What's next

This notebook documents one complete modelling iteration. Natural next directions:

- UID-style aggregation features (constructing actor-level behavioural baselines from probable user identifiers)
- Threshold / precision-recall analysis for production operating-point selection
- Cohort-level performance analysis (e.g., breakdown by product code, card network, transaction-amount bucket)
