# BT4012 Illicit-Transaction Detection

Notebooks for the BT4012 Kaggle competition: ranking illicit Bitcoin transactions in an Elliptic-derived dataset. Training covers time steps 1–35 and the test covers steps 36–49; the metric is pooled ROC AUC.

Best public score: **0.95162** (τ = 5 LightGBM retrained with cross-view pseudo-labels), with **0.94992** (plain τ = 5 LightGBM) as the second final pick.

## Notebooks

| Notebook | What it is | Use it to |
|---|---|---|
| `4012_success_story.ipynb` | Only the steps that produced the two final submissions, in the order they were found. Failed and parallel experiments are left out. | Reproduce the two final files and read the main line of reasoning (about 68 cells). |
| `4012_combined_full.ipynb` | Every experiment from the project's four working notebooks (original, graph, operation-balanced, alternate) in one run order, Steps 1–44 (382 cells). | Look up any method we tried, its result and why it was kept or dropped. |

### Step map for the full notebook

| Steps | Source notebook | Content |
|---|---|---|
| Setup, exploration, cleaning, Steps 1–27, final submissions | original | Baseline, recency weighting (Step 3f), feature, graph, learner, novelty and blending experiments |
| 28 (28a–28g) | graph exploration | Graph features, GNNs and unlabelled rows |
| 29 (29a–29f) | alternate | New exploration (T1–T3), revised cleaning, boundary-window validation |
| 30 (30a–30e) | operation-balanced | Revised Evaluation v2, Louvain operation proxies, operation-balanced weights |
| 31–44 | alternate | Diagnostics, importance weighting, Evaluation v2 (35), cross-view pseudo-labelling (41), final sweeps and the two-view blend (43–44) |

Text inside the Step 29–44 cells keeps the source notebooks' numbers (e.g. "Step 8b"); the table at the top of the full notebook maps them to the new step numbers.

The success-story notebook keeps the source numbers (Part 1 = original Steps 1–3g, Part 2 = alternate Steps 1–8).

## Data files

Download these four files from the competition's Kaggle data page and put them in one folder:

| File | Contents |
|---|---|
| `train.csv` | `txId`, `time_step` (1–35), `label` (illicit / licit / unknown), `feat_1`–`feat_165` |
| `test.csv` | `index` (submission ID), `txId`, `time_step` (36–49), `feat_1`–`feat_165` |
| `txs_edgelist.csv` | Directed transaction graph (two id columns) |
| `sample_submission.csv` | Submission template (`index,target`) |

The data files are not stored in this repository.

## Running the notebooks

Both notebooks are written for **Google Colab** with the data on Google Drive.

1. Upload the four data files to `MyDrive/bt-4012-competition-2026/` (the default data folder).
2. Open a notebook in Colab and run it **top to bottom**. The first cell mounts Drive.
3. To use a different folder:
   - success story: set the environment variable `BT4012_DATA_DIR` before the first cell;
   - full notebook: edit `DATA_DIR` in the first code cell (it is hard-coded there).

Running locally works too: the Drive mount is skipped automatically, but set the data folder as above and change `SUB_DIR` (default `/content/submissions`) to a local path.

### Outputs and caching

- **Submission files** are written to `/content/submissions/`. The success story writes `sub_r0_base.csv`, `sub_r0_base_tau5.csv` (pick 2) and `sub_r0_old_tau5_8b_cross_view_q0.3.csv` (pick 1).
- **Model cache:** every fit is cached in `<data folder>/cache/`, keyed by features, parameters, windows, weights and data. Re-running loads fits instantly; set `FORCE_RETRAIN = True` (in the baseline cells) to retrain everything. Both notebooks use the same cache names, so a cache built by one serves the other.
- **Intermediate files:** the notebooks write `feature_stability.csv` and `feature_stability_t3.csv` themselves; later cells read them, so don't skip the exploration cells.
- **Order matters:** in the success story, Part 2 reads Part 1's τ = 5 file (`sub_r0_base_tau5.csv`) and stability table.

### Runtime

- **First run is slow:** both notebooks train many 5-seed configurations, so a run from an empty cache takes far longer than one with a warm cache. The full notebook is much longer than the success story. Each training cell prints its own elapsed time.
- **GPU:** the GNN cells (Step 28e) benefit from a GPU runtime.

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
