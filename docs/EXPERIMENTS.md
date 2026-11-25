# Experiment Configurations

This repository contains two distinct experiment records: the five configurations in the supplied Jupyter notebook and the broader experiment design/results reported in the paper. They must not be conflated.

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

The paper describes eight task/dataset/model cases across the two translation directions, two dataset sizes, and two U-Net approaches. Its result tables are transcribed in [PAPER_STUDY.md](PAPER_STUDY.md).

### Paper-reported T1WI → FLAIR results

| Dataset | Model | PSNR (dB) | MAE | MSE |
|---|---|---:|---:|---:|
| 342 slices | Proposed U-Net | 22.444 | 0.032 | 0.005 |
| 342 slices | U-Net from [21] | 20.731 | 0.038 | 0.008 |
| 685 slices | Proposed U-Net | 22.577 | 0.035 | 0.005 |
| 685 slices | U-Net from [21] | 21.960 | 0.038 | 0.006 |

### Paper-reported T2WI → FLAIR results

| Dataset | Model | PSNR (dB) | MAE | MSE |
|---|---|---:|---:|---:|
| 342 slices | Proposed U-Net | 25.3080 | 0.0180 | 0.0020 |
| 342 slices | U-Net from [21] | 25.2670 | 0.0200 | 0.0030 |
| 685 slices | Proposed U-Net | 26.3570 | 0.0140 | 0.0020 |
| 685 slices | U-Net from [21] | 26.2710 | 0.0150 | 0.0020 |

**Provenance:** these are values reported in Tables II and III of the paper, not reproduced outputs from this repository's notebook. The notebook does not currently implement T2WI → FLAIR, the paper's 80/10/10 test protocol, or the paper's two-model comparison.

## Reporting checklist for new runs

- Variant/model and complete hyperparameters
- Number of subjects and slices
- Patient-level split details, if used
- Per-subject and aggregate MSE, MAE, and PSNR
- Software versions and hardware
- Any deviations from the supplied notebook or paper
- Whether metrics are validation or held-out test metrics

Do not publish illustrative or unverified metric values as experimental results.
