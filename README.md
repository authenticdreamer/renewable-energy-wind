# How to get started

1. **Clone repo and download all data of the following websites**:
  - Two Brazilian datasets: https://zenodo.org/record/1475197#.ZD6iMxXP2WC
  - Uk dataset: https://zenodo.org/record/5841834#.ZEajKXbP2BQ
    
2. **Store data in `data/raw` folder** such that it looks like this:
<p align="center">
<img src="fig/structure_of_data_folder.png" width="300">
</p>


3. **To get an overview start by looking at the `doc` folder**
    - Run `create_processed_data.ipynb` once in the beginning
    - There is one main IPython notebook per farm containing the high-level call of functions and the results (called `..._main`)

4. **`Exp` folder** contains experiments and data exploration


# Summary of task, approach and results 
- **Task**: There are three wind farms. Predict the power generated by one turbine per wind farm for the next step (=10min), next hour and next day

- **Givens**: 3 datasets 
    - UK: Kelmarsh wind farm (=kwf) Shape: (288900,299) per turbine
    - Brazil: Beberide wind farm (=uebb) Shape: (52560,40) per turbine with 5 extra height dimension
    - Brazil: Pedra do Sal wind farm (=ueps) Shape (52560,48) with 6 extra height and 26 range dimensions

- **Approach**: More or less the same approach taken for all three datasets. For each dataset: 
    1. Parse data to .csv and ignore all data but the turbine of interest (see `data_loader.py`)
    2. Make time-series data a supervised problem and generate smart features (see `preprocessing.py`)
    3. Train ordinary linear least squares (=OLS) on scaled data for each farm and each prediction horizon (i.e., 3 farms * 3 predictions = 9 models) (see `model.py`)
    4. Try out different approaches with HPO, if the benchmark was not beaten by OLS (see `model.py`)
    5. Visualize results (see `visualizations.py`)

A **more detailed** documentation of the approach can be found in the `src` folder in the docstring of the functions

## Results

The benchmark was slightly beaten on all wind farms for all horizons. Xgboost worked best for Kelmarsh wind farm, whereas a simple OLS was enough to beat the benchmark for Bebride (and Ueps) wind farm. As an example, below you can find the performance metrics of all wind farms as well as an examplatory plot of the ground truth vs the predcition for Beberide wind farm

| Model Name              |    RMSE |   Benchmark_RMSE |      MAE |   Benchmark_MAE |
|:------------------------|--------:|-----------------:|---------:|----------------:|
| Kelmarsh 10min horizon  | 142.219 |          145.603 |  89.3621 |         91.5538 |
| Kelmarsh 1 hour horizon | 247.4   |          263.749 | 169.839  |        183.286  |
| Kelmarsh 1 day horizon  | 596.683 |          623.023 | 476.414  |        510.71   |
|:------------------------|--------:|-----------------:|---------:|----------------:|
| Beberide 10min horizon  |  52.419 |          55.4172 |  34.4326 |         36.245  |
| Beberide 1 hour horizon | 111.705 |         119.25   |  79.5705 |         81.9437 |
| Beberide 1 day horizon  | 178.472 |         196.742  | 130.307  |        151.508  |
|:------------------------|--------:|-----------------:|---------:|----------------:|
| Ueps 10min horizon      |  44.4573|              nan |  28.3013 |             nan |
| Ueps 1 hour horizon     | 107.566 |              nan |  76.107  |             nan |
| Ueps 1 day horizon      | 207.585 |              nan | 163.609  |             nan |


<p align="center">
<img src="/fig/Beberide_comparison_pred_truth.png" width="900">
</p>

## Interpretation of results

As expected, the models are best at classifying for the shortest horizon and except for Kelmarsh wind farm do reasonably well on the longer horizons, considering that the model knows no weather forecast or any other additional information from the future. The examplatory plot shows, that the model was able to pick up the daily wind cycles for all three horizons.

## Outlook

As this is only a seminar project, there are many areas with the potential to improve the metrics. For me, it seems most promising to next look at:
- Preprocessing: 
    1. Investigate horizontal lines in wind-power plots
    2. Investigate dimensionality reduction techniques
        - PCA, Correlations, fancyimpute, ...
    3. Design smarter features
- Try to find better model:
    - DNN, especially RNNs, ...
