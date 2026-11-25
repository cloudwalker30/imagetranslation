# Experiment Configurations

This repository documents two distinct experiment descriptions: the five configurations in the supplied Jupyter notebook and the broader experiment design reported in the paper. They must not be conflated.

## Notebook configurations

The supplied notebook contains five U-Net variants.

| Variant | Output activation | Validation fraction | Epoch limit | Batch size | Early stopping |
|---|---|---:|---:|---:|---|
| 1 | ReLU | 0.20 | 30 | 4 | No |
| 2 | Sigmoid | 0.20 | 150 | 4 | Yes |
| 3 | ReLU | 0.20 | 150 | 4 | Yes |
| 4 | ReLU | 0.20 | 150 | 4 | Yes |
| 5 | ReLU | 0.30 | 50 | 4 | Yes |

All variants use Adam, mean squared error loss, and mean absolute error as a metric. The split uses `random_state=2`. Variants 2–5 configure `EarlyStopping(monitor='loss', patience=5)`.

The notebook calculates MSE, MAE, and PSNR with peak intensity 1.0. No verified numerical outputs from these notebook runs are included here.

## Paper experiment design

The paper “Inter-modality MRI Image Translation using Deep Learning” describes:

- Two tasks: T1WI → FLAIR and T2WI → FLAIR.
- Two paired dataset sizes: 342 slices (50% dataset) and 685 slices (full dataset).
- An 80% training / 10% validation / 10% testing split.
- Two U-Net approaches: the proposed model and the U-Net adapted from reference [21].
- Evaluation at 150 epochs on unseen test data.
- Metrics: PSNR, MAE, and MSE.

The paper describes eight task/dataset/model cases across the two translation directions, two dataset sizes, and two U-Net approaches. The paper's evaluation metrics are described above; numerical result tables are intentionally omitted from this repository documentation.\n\n## Reporting checklist for new runs

- Variant/model and complete hyperparameters
- Number of subjects and slices
- Patient-level split details, if used
- Per-subject and aggregate MSE, MAE, and PSNR
- Software versions and hardware
- Any deviations from the supplied notebook or paper
- Whether metrics are validation or held-out test metrics

Do not publish illustrative or unverified metric values as experimental results.
