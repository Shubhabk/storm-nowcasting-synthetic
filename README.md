# storm-nowcasting-synthetic
# Synthetic Storm Cell Nowcasting (Variables + Training Code)

This repository contains the **complete, reproducible code** for a systematic nowcasting study on synthetic convective storm cells.

Using **only the first 10–20 minutes** of each storm’s observed evolution, four regression models  
(Gradient Boosting, XGBoost, Random Forest, Ridge) predict:  
- Total lifetime (hours)  
- Peak rainfall rate (mm/hr)  
- Total accumulated rainfall (mm)

The six experiments (3 scenarios × 2 experiments each) systematically increase complexity and clearly demonstrate how predictive skill changes with the balance of **deterministic signal vs. stochastic noise** — from near-perfect **R² ≈ 1.0** in highly controlled cases to degradation under full randomness.


**Data Generation Summary**  
The folder  includes **three scenarios** of increasing complexity, with **two experiments each** (6 notebooks in total under `01_data_generation/`):  
- **Scenario 1** — Fixed Intensity
- Experiment 1: Master Intensity Profile with Slight Variability
- Experiment 2: Fixed Peak Intensity, Shorter Lifetime
- **Scenario 2** — Fixed lifetime
- Experiment 1: Predictable Intensity with Fixed Lifetime
- Experiment 2: Unique Peak Intensity with Fixed Lifetime  
- **Scenario 3** — All parameters fully variable
- Experiment 1: Fully Variable Intensity and Derived Features
- Experiment 2: Unique Intensity Profiles with Persistent Motion

### Key Variables (Targets & Main Features)

| Variable           | Description                                    | Unit      |
|--------------------|------------------------------------------------|-----------|
| `lifetime`         | Total storm duration                           | hours     |
| `peak_rainfall`    | Maximum rainfall rate during lifecycle         | mm/hr     |
| `total_rainfall`   | Integrated rainfall over entire lifecycle      | mm        |
| `intensity`        | Radar reflectivity                             | dBZ       |
| `size_pixels`      | Spatial extent of storm cell                   | pixels    |
| `rainfall_rate`    | Instantaneous rainfall rate                    | mm/hr     |
| `pos_x`, `pos_y`   | Geographic position                            | arbitrary |
| `vx`, `vy`         | Velocity components                            | units/5min|

Early-stage features (first 10–20 min) used for nowcasting:
- initial, max, mean, std of intensity/rainfall/size
- growth slopes
- statistical summaries + polynomial interactions (scenario 3)

### Training Code Overview

`02_training/` contains all 6 experiments (3 scenarios × 2 expts each).

Main pipeline:
```python
1. Load synthetic tracks (50k storms per scenario)
2. Extract features from observation window (10 or 20 min)
3. Mutual-information feature selection (top 10–25)
4. Standard-scale features
5. Train 4 models with 5-fold CV + grid search:
   - GradientBoostingRegressor
   - RandomForestRegressor  
   - XGBRegressor
   - Ridge
6. Evaluate on hold-out test set (20k storms)
   → Metrics: R², RMSE, MAE, MAPE
