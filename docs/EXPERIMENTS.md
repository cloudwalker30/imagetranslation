# Experiment Configurations

The supplied notebook contains five U-Net variants.

| Variant | Output activation | Validation fraction | Epoch limit | Batch size | Early stopping |
|---|---|---:|---:|---:|---|
| 1 | ReLU | 0.20 | 30 | 4 | No |
| 2 | Sigmoid | 0.20 | 150 | 4 | Yes |
| 3 | ReLU | 0.20 | 150 | 4 | Yes |
| 4 | ReLU | 0.20 | 150 | 4 | Yes |
| 5 | ReLU | 0.30 | 50 | 4 | Yes |

All variants use Adam, mean squared error loss, and mean absolute error as a metric. The train/validation split uses `random_state=2). Variants 2–5 configure `EarlyStopping(monitor='loss', patience=5)`.

## Results reporting

The notebook calculates MSE, MAE, and PSNR with peak intensity 1.0. No numerical results are included here because verified experiment outputs were not provided.

For each run, record:
- Variant and complete hyperparameters
- Number of subjects and slices
- Patient-level split details, if used
- Per-subject and aggregate MSE, MAE, and PSNR
- Software versions and hardware
- Any deviations from the supplied notebook
