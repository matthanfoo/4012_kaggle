# BT4012 Illicit-Transaction Detection

Notebooks for the BT4012 Kaggle competition: ranking illicit Bitcoin transactions in an Elliptic-derived dataset. Training covers time steps 1–35 and the test covers steps 36–49; the metric is pooled ROC AUC.

Best public score: **0.95162** (τ = 5 LightGBM retrained with cross-view pseudo-labels), with **0.94992** (plain τ = 5 LightGBM) as the second final pick.

## Notebooks

| Notebook | What it is | Use it to |
|---|---|---|
| `e1113763.ipynb` | Only the steps that produced the two final submissions, in the order they were found. Failed and parallel experiments are left out. | Reproduce the two final files and read the main line of reasoning (about 68 cells). |
| `4012_combined_full.ipynb` | Every experiment from the project's four working notebooks (original, graph, operation-balanced, alternate) in one run order, Steps 1–44 (382 cells). | Look up any method we tried, its result and why it was kept or dropped. |

### Step map for the full notebook

In the full notebook, text inside the Step 29–44 cells keeps the source notebooks' numbers (e.g. "Step 8b"); the table at the top of the full notebook maps them to the new step numbers.
In the main notebook, source numbers are kept within parts (Part 1 = original Steps 1–3g, Part 2 = alternate Steps 1–8).

## Data files

Download these four files from the competition's Kaggle data page and put them in one folder:

## Running the notebooks

Both notebooks are written for **Google Colab** with the data on Google Drive.

1. Upload the four data files to `MyDrive/bt-4012-competition-2026/` (the default data folder).
2. Open a notebook in Colab and run it **top to bottom**. The first cell mounts Drive.
3. To use a different folder:
   - main: set the environment variable `BT4012_DATA_DIR` before the first cell;
   - full notebook: edit `DATA_DIR` in the first code cell (it is hard-coded there).

Running locally works too: the Drive mount is skipped automatically, but set the data folder as above and change `SUB_DIR` (default `/content/submissions`) to a local path.

## Requirements

Python 3.10+ with:

| Package | Needed by |
|---|---|
| `numpy`, `pandas`, `scipy`, `scikit-learn`, `matplotlib` | both |
| `lightgbm` (4.6.0) | both |
| `networkx` | full (Louvain communities) |
| `xgboost`, `catboost` | full (learner screens; the notebook runs `pip install catboost`) |
| `torch` | full (MLP / GraphSAGE, Step 28e) |

Colab includes everything except `catboost`.

The submitted files were produced with **LightGBM 4.6.0** and `deterministic=True`; other versions may change trees slightly, so rebuilt files can differ from the leaderboard scores in the last decimals.

## Notes

- **Full notebook outputs:** these are copied from each source notebook's own run, and the combined notebook has not been run as a whole. Cells marked "combined notebook" restore shared state between sections (see the notes at the top of the notebook).
- **Pseudo-labels:** the cross-view pseudo-labelling uses the unlabelled test features only; no test labels are used anywhere.
- **Leaderboard scores** quoted in the notebooks are public scores (30% of the test set).
