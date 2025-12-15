
# Water Depth Prediction (Harvey)

A compact pipeline to predict block-group water depth from event- and terrain-related features using PyTorch, with baselines from scikit-learn. Includes data cleaning, feature/target scaling, an FNN with residual blocks, early stopping, and reproducible evaluation.

## Dataset
- Source file: `data/harvey_observed_waterDepth.csv` (local, not committed).
- Key features used in the notebook: `Harvey_Totals`, `harvey_Wind`, `harvey_TMax`, `harvey_TMin`, `harvey_Pre`, `harvey_CERA`, `Elevation` and target `waterDepth`. [1](https://tamucs-my.sharepoint.com/personal/piyalong_tamu_edu)%20-%20JupyterLab.pdf)

## Data cleaning
- Removed **non-positive** targets and capped upper tail by removing values above the **99th percentile** (threshold ≈ 12.0). Final size ≈ **2,857** records after filtering. [1](https://tamucs-my.sharepoint.com/personal/piyalong_tamu_edu)%20-%20JupyterLab.pdf)

## Modeling
- **Neural net**: Feedforward NN (hidden_dim=256) with ReLU; trained with **Adam (lr=1e-4)** and **L1 loss**; **early stopping** with patience=10 across a max of 20,000 epochs; best weights restored. [1](https://tamucs-my.sharepoint.com/personal/piyalong_tamu_edu)%20-%20JupyterLab.pdf)
- **Baseline**: Linear Regression fitted on the same split. [1](https://tamucs-my.sharepoint.com/personal/piyalong_tamu_edu)%20-%20JupyterLab.pdf)

## Results (Test set)
- FNN: **R² = 0.1621**, **MAE = 0.7349**, **RMSE = 1.4321**, **Pearson r = 0.4315 (p ≈ 2.41e-27)**. [1](https://tamucs-my.sharepoint.com/personal/piyalong_tamu_edu)%20-%20JupyterLab.pdf)
- Linear: **R² = 0.0180**, **MAE = 0.8935**, **RMSE = 1.5504**, **Pearson r ≈ 0.1623**. [1](https://tamucs-my.sharepoint.com/personal/piyalong_tamu_edu)%20-%20JupyterLab.pdf)

!Predicted vs Actual

> The diagonal “perfect fit” line is overlaid; points cluster near the line with notable spread. Figure exported in the notebook as `predicted_vs_actual_water_depth.png`. [1](https://tamucs-my.sharepoint.com/personal/piyalong_tamu_edu)%20-%20JupyterLab.pdf)

## Quickstart

```bash
# Python 3.10+ recommended
python -m venv .venv
source .venv/bin/activate               # Windows: .venv\Scripts\activate
pip install -r requirements.txt

# Put harvey_observed_waterDepth.csv under data/
python src/train.py --data data/harvey_observed_waterDepth.csv \
                    --hidden-dim 256 --lr 1e-4 --patience 10 \
                    --remove-upper-quantile 0.99 --remove-nonpositive

