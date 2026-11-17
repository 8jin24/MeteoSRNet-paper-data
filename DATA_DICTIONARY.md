# Data dictionary

## Common identifiers and metrics

| Field | Meaning / unit |
| --- | --- |
| `model`, `comparison` | Model identifier defined in README. `comparison` identifies the baseline compared with MeteoSRNet. |
| `timestamp_utc` | Hourly timestamp in UTC, ISO 8601 format. |
| `epoch`, `selected_best_epoch`, `original_best_epoch`, `retained_winner_epoch` | One-based training epoch. Blank selected epoch for bilinear means not applicable. |
| `trained_epochs` | Completed training epochs; zero for bilinear. |
| `parameters` | Number of trainable parameters. |
| `mae_celsius`, `rmse_celsius`, `bias_celsius` | MAE, RMSE and mean signed error (prediction minus target), degrees Celsius. |
| `ssim` | Structural similarity, dimensionless. |
| `psnr_db` | Peak signal-to-noise ratio, decibels; common target range stated in README. |
| `samples` | Number of hourly full-field samples in the group. |
| `month`, `season` | Month number and seasonal label. |
| `temperature_category` | `cold`, `normal` or `hot`, based on target full-field mean temperature. |
| `high_gradient` | Boolean flag for target spatial variation intensity meeting the training-year threshold. |
| `coarse_mae_celsius`, `coarse_rmse_celsius`, `coarse_max_abs_celsius` | Error between the block-averaged reconstruction and low-resolution input, degrees Celsius. |
| `evaluation_seconds` | Recorded evaluation elapsed time, seconds. |
| `best_validation_rmse_celsius` | Original checkpoint-selection score; its aggregation depends on model family as described in README. |

## Training histories

`epoch` ranges from 1 to 40. `train_l1_normalized` is the recorded L1 training loss in normalized units. `val_mae_celsius` and `val_rmse_celsius` are original validation log values. `epoch_seconds` is the recorded epoch duration. `peak_allocated_mib` is peak allocated GPU memory in MiB. The latter two fields are absent from the original MeteoSRNet history and have not been filled in.

## Unified validation and checkpoint provenance

| Field | Meaning |
| --- | --- |
| `kind` | Source training family; `ablation` for P4 records its original training entry point. |
| `label`, `checkpoint` | `original_best` or `last`, identifying the original selection or epoch-40 checkpoint. |
| `path` | Original project-relative checkpoint path, not a download link or a member of this package. |
| `state_sha256`, `file_sha256` | Original model-state or checkpoint-file checksum. |
| `last_epoch` | Epoch of the final checkpoint (40). |
| `original_best_global_rmse`, `last_global_rmse` | Global validation RMSE of the two retained candidates, degrees Celsius. |
| `retained_winner` | Candidate selected by comparing the saved candidates. |
| `last_minus_original_best_global_rmse` | Final candidate minus original candidate global RMSE, degrees Celsius. |
| `identical_weights` | Whether the two model-state checksums are identical. |
| `inference_batch_size`, `original_validation_batch_size` | Unified re-evaluation batch size and original validation batch size. |
| `global_rmse_celsius`, `global_mae_celsius` | Metrics aggregated across the whole validation set, degrees Celsius. |
| `mean_batch_rmse_celsius`, `mean_batch_mae_celsius` | Arithmetic averages of per-batch metrics, degrees Celsius. |
| `logged_validation_score`, `logged_score_definition` | Original recorded score and its aggregation definition. |
| `recomputed_legacy_minus_logged` | Recomputed original-definition score minus logged score, degrees Celsius. Here `legacy` means original aggregation, not a 20-epoch experiment. |
| `mse_celsius_squared`, `mae_celsius` | Per-hour validation MSE in squared degrees Celsius and MAE in degrees Celsius. |

## Statistical summaries

`block_days` is the circular block length. `replicates` is 2,000. `delta_rmse_other_minus_full` is RMSE(other) minus RMSE(MeteoSRNet), in degrees Celsius. `ci_low` and `ci_high` are the corresponding percentile limits. `hourly_win_fraction_other` is a fraction from 0 to 1, not a percentage.

In `hourly_error_quantiles.csv`, `p5`, `p50`, `p90`, `p95` and `p99` mean the 5th, 50th, 90th, 95th and 99th percentiles of hourly spatial RMSE, in degrees Celsius. The column `p5` is a percentile, not a progressive P5 model. `max` is maximum hourly spatial RMSE.

In `annual_error_decomposition.csv`, `annual_mean_field_rmse` is the spatial RMSE of annual mean fields, `hourly_global_rmse` is the full-year hourly aggregate RMSE, both in degrees Celsius. `persistent_mse_fraction` is the squared annual-mean-field RMSE divided by the squared hourly global RMSE, dimensionless.

## Spatial arrays and JSON files

`comparison_annual_mean_best_models_fields.npz` contains `latitude` and `longitude` coordinate vectors (degrees), `hr` (96 x 96), `lr` (24 x 24), and six method fields (96 x 96). Temperature arrays are annual means in degrees Celsius. The companion metadata gives year, sample count and selected epochs.

`selected_cases.npz` contains three cases: `hr` and the three method arrays `bilinear`, `swinir_light`, `meteo_sr` have shape (3, 96, 96), and `lr` has shape (3, 24, 24). Temperatures are in degrees Celsius. Coordinate vectors are shared. `selected_cases.json` records the selection rule, zero-based hourly indices, UTC timestamps and reference hourly RMSEs. Case order is near the median, near the 95th percentile and maximum hourly RMSE of MeteoSRNet. Differences plotted in Fig. 7 are method fields minus `hr`.

`normalization.json` gives `variable`, `train_year`, `unit`, `mean` and `std`. The mean and standard deviation are in degrees Celsius. Normalized temperature is (temperature - mean) / std.
