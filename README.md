# storm-nowcasting-synthetic
# Synthetic Storm Cell Nowcasting (Variables + Training Code)

This repository contains only the **core variable definitions** and **training pipeline** used in the synthetic storm nowcasting experiments.  
No full report or data files are included.

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

`training/` contains all 6 experiments (3 scenarios × 2 expts each).

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
