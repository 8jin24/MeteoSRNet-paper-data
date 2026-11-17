# MeteoSRNet paper supporting data

## Download

[Download the complete data package (v1.0.0)](https://github.com/8jin24/MeteoSRNet-paper-data/releases/download/v1.0.0/MeteoSRNet_paper_data_20261117.zip) or view the [versioned release](https://github.com/8jin24/MeteoSRNet-paper-data/releases/tag/v1.0.0). The ZIP contains 31 data files and four documentation and checksum files. Extract the ZIP to access the CSV, JSON and NPZ files described below.

The repository provides English documentation. The data package excludes raw NetCDF files, model weights and program code. The original ERA5 download address is provided in the manuscript and repeated below.

Supporting data for **Residual Reconstruction and Progressive Structural Evaluation for 4-fold Super-Resolution of a 2-Meter Temperature Field**.

Data snapshot: 2026-11-17. This package contains 31 data files, a data dictionary, a source manifest and SHA-256 checksums. All files use descriptive names in one directory. Numerical values retain the precision of the saved experiment records.

## Original ERA5 data

The original ERA5 download address is provided in the manuscript's **Data availability** section. The raw NetCDF files are not included in this package.

The address reported in the manuscript is https://cds.climate.copernicus.eu/datasets/reanalysis-era5-single-levels?tab=download . The dataset DOI is https://doi.org/10.24381/cds.adbb2d47 .

The study uses hourly 2-metre temperature (`t2m`) from 2023 to 2025, over 100-123.75 degrees east and 16.25-40 degrees north, on a 0.25-degree grid. The 96 x 96 target grid is stored north-to-south and west-to-east. The splits are 2023 for training (8,760 hours), 2024 for validation (8,784 hours), and 2025 for testing (8,760 hours). Temperatures are converted from kelvin to degrees Celsius by subtracting 273.15. Non-overlapping 4 x 4 arithmetic averaging produces 24 x 24 inputs. The mean and standard deviation in `normalization.json` are calculated from the training year. No geographic area weighting is used.

## Data and paper correspondence

| Files | Paper content |
| --- | --- |
| `comparison_metrics_summary.csv`, `comparison_test_per_sample_metrics.csv` | Table 2, Fig. 3 and hourly test errors |
| `comparison_monthly_metrics.csv`, `comparison_seasonal_metrics.csv`, `comparison_temperature_category_metrics.csv`, `comparison_gradient_category_metrics.csv` | Table 3 and Fig. 4 |
| `comparison_coarse_consistency_metrics.csv` | Coarse-grid consistency results |
| `progressive_metrics_summary.csv`, `progressive_test_per_sample_metrics.csv` | P0-P4 in Table 4 and Fig. 5 |
| `training_history_*.csv` | Ten configurations trained for 40 epochs, Supplementary Table S2; `training_history_meteo_sr.csv` supports Fig. 2 |
| `retained_checkpoint_comparison.csv`, `validation_scores.csv`, `validation_per_sample.csv`, `checkpoint_inventory.csv` | Supplementary Table S1 |
| `paired_block_bootstrap.csv` | Supplementary Table S3 (7-day blocks) and 1-/14-day block sensitivity results |
| `hourly_error_quantiles.csv`, `annual_error_decomposition.csv` | Hourly error distributions and annual mean error decomposition |
| `comparison_annual_mean_best_models_fields.npz`, `comparison_annual_mean_best_models_metadata.json` | Fig. 6 and Supplementary Figs. S1 and S2 |
| `selected_cases.npz`, `selected_cases.json` | Fig. 7 |
| `normalization.json` | Training-year normalization parameters |

## Model identifiers

The main comparison uses `bilinear`, `srcnn`, `edsr_baseline`, `unet_sr`, `swinir_light` and `meteo_sr`. The progressive ablation uses `p0` through `p4`. P4 uses the existing structurally equivalent run originally named `no_channel_attention`. In the published validation tables its model identifier is normalized to `p4`. Original checkpoint paths and the manifest retain this source name to identify the actual run. P4 is not an additional training run.

The selected epochs are MeteoSRNet 38, SRCNN 34, EDSR-baseline 32, U-Net-SR 40, SwinIR-light 29, P0 31, P1 36, P2 29, P3 39 and P4 38. All histories contain epochs 1 through 40.

## Metric interpretation

The test summaries use accumulated errors over the complete test set. The original validation criterion for the four external learning models is global RMSE. For MeteoSRNet and P0-P4, the logged criterion is the arithmetic mean of batch RMSEs, including an equally weighted final partial batch. `best_validation_rmse_celsius` and the histories retain these original criteria. The `global_rmse_celsius` values in `validation_scores.csv` use the unified full-validation-set calculation. These are different fields with different meanings.

Annual RMSE can be recovered from equally sized hourly fields as `sqrt(mean(hourly_rmse**2))`; it is not the arithmetic mean of hourly RMSE. MAE, bias and SSIM are averaged over hourly values. PSNR uses the common target range of 85.79025268554688 degrees Celsius. SSIM uses an 11 x 11 uniform averaging window, reflection padding, K1=0.01 and K2=0.03.

The target-defined thresholds calculated from 2023 are: cold hourly field-mean temperature at or below 6.225454807281494 degrees Celsius; hot hourly field-mean temperature at or above 27.516786575317383 degrees Celsius; and high spatial variation intensity at least 0.7334275245666504 degrees Celsius. Spatial variation intensity is half the sum of the mean absolute horizontal and vertical adjacent-grid temperature differences. These thresholds are applied to the 2025 targets. Seasons are winter (December-February), spring (March-May), summer (June-August), and autumn (September-November).

The bootstrap file contains three baselines at block lengths 1, 7 and 14 days, with 2,000 replicates for each. Hourly squared RMSE is first averaged within each of 365 days. Circular blocks use the same sampled days across methods. The NumPy random generator seed is `20260912 + block_days`. The reported limits are the 2.5th and 97.5th percentiles of RMSE(other) minus RMSE(MeteoSRNet). They describe temporal sampling uncertainty conditional on the fitted models. Intervals are not adjusted for multiple comparisons. The hourly win fraction counts hours with strictly lower RMSE for the other model.

## Files and provenance

CSV files contain headers and can be opened with standard CSV readers. NPZ files contain named NumPy arrays and can be read using `numpy.load(path, allow_pickle=False)`. JSON files contain metadata. Units and field definitions are in `DATA_DICTIONARY.md`.

`MANIFEST.csv` maps all 31 data files to original project-relative source names, source hashes, published hashes, row counts and transformations. `SHA256SUMS.txt` checks all package files except itself. Checkpoint paths in the validation files identify source artifacts; the weight files are not part of this data package. `state_sha256` and `file_sha256` refer to those source checkpoints. The normalization file omits local cache paths and modification timestamps while preserving the original normalization values.

Only the paper's configurations and plotted case arrays are included. This package contains data for checking the reported analyses. Model code and checkpoints are separate materials. Figure 1 is an architecture illustration rather than a measured data product.
