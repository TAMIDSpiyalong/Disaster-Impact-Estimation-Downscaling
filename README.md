
# DIEP Water Depth Downscaling (work in progress)

## Introduction
This project focuses on downscaling disaster impact predictions (DIEP) from aggregated levels (e.g., zip codes) to finer spatial units (e.g., census block groups) by weather data and social media input. Building on prior work that demonstrated the effectiveness of neural networks for predicting FEMA-defined flood damage categories using geolocated social media, this effort aims to enhance spatial resolution and predictive accuracy. The approach leverages historical storm datasets—including Hurricanes Harvey, Imelda, and Beryl—alongside climate variables (precipitation, wind speed, elevation) and inundation data. 

## Data Collection
- Source file: `“Hurricane”_observed_waterDepth.csv`.
- Key features used in the notebook: `Hurricane_Totals`, `Hurricane_Wind`, `Hurricane_TMax`, `Hurricane_TMin`, `Hurricane_Pre`, `Hurricane_CERA`, `Elevation` and target `waterDepth`.
- The zip code boundaries were restructured following the 2020 U.S. Census tabulation blocks. Storms occurring prior to 2020 were calculated using the 2010 boundaries, while those post 2020 utilized the 2020 boundaries. Both versions of zip code polygons were obtained from the ESRI Federal Data account hosted on Arcgis.com and are part of the National Geospatial Data Asset (NGDA) Portfolio Datasets.
- `Hurricane_Totals` Using the PRISM Climate Group database, precipitation raster for each day of the incident dates. Raster were then clipped to a boundary polygon of the state of Texas with the clipping extent maintained. Raster Calculator was used to add all days for a storm together to get total precipitation for the duration. National Centers for Environmental Information (NCEI) collect data at weather/climate stations, from radars, weather balloons, moored buoys in the ocean, ships, airplanes, satellites, and from computer models. Data provided are for tropical weather events (hurricanes and tropical storms) associated with FEMA-declared disasters in Texas from 2010-2024. NCEI data were provided for all NOAA climate divisions in Texas (High Plains 4801, Low Rolling Plains 4802, North Central 4803, East Texas 4804, Trans Pecos 4805, Edwards Plateau 4806, South Central 4807, Upper Coast 4808, Southern 4809, Lower Valley 4810). NCEI weather Station data was pivoted to create a table with one record per station ID. Minimum temperature, maximum wind speed, maximum temperature, and sum of all precipitation for the duration of the storm were calculated. For temperature and wind raster, only points with values >0 was used in IDW. 
- `Hurricane_Wind`, `Hurricane_TMax`, `Hurricane_TMin`, `Hurricane_Pre` Wind speed probabilities were obtained from the National Hurricane Center GIS Archive.  Data downloaded was in a 50-knot format for each storm date period. Files were first converted from KMZ form to ESRI polygon layers in ArcPro. These polygons were then converted to raster, values were reclassified to ensure areas with no values had a value of “0”, and the Cell Statistics tool was used to calculate the maximum, mean, and sum wind speed value for each cell over the duration of the storm event. Inundation data was sourced from the Louisiana State University Coastal Emergency Risks Assessment database. Measurements represented the maximum water levels in feet above ground as referenced to mean sea level (MSL). Each storm had one raster representing maximum level throughout the duration of the event.
- `Hurricane_CERA`
- `Elevation`  
- `waterDepth` Water depth data is a small integer in inch collected from OpenFEMA dataset. The definition is the depth of flood water in inches. Note: there are instances where measurements were provided in feet. This water depth has zip code, census tract, census block, and census block group information.
- Dataset available at DOI: https://doi.org/10.7266/n90stx2y.


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

