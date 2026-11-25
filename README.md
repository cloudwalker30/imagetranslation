# Inter-modality MRI Image Translation Using Deep Learning

**Academic research implementation and paper companion**

This repository contains a notebook implementing paired 2D T1-weighted MRI (T1WI) to FLAIR MRI translation using a U-Net-style convolutional neural network, alongside research documentation for the broader study **“Inter-modality MRI Image Translation using Deep Learning”** by Prachee Patel, Dr. Pariza Kamboj, and Dr. Nirali Nanavati.

> **Research and educational use only.** This code is not a medical device. Synthesized images must not be used for diagnosis, treatment planning, or clinical decision-making.

## Research scope

The paper investigates two translation tasks:

- T1WI → FLAIR
- T2WI → FLAIR

It reports paired DICOM data, U-Net-based models, and PSNR, MAE, and MSE evaluation. See [the paper study summary](docs/PAPER_STUDY.md) for the reported dataset, preprocessing, model dropout settings, experiment design, and published result tables.

**Important distinction:** the notebook in this repository implements only the T1WI → FLAIR workflow and contains five notebook-specific model configurations. It does not reproduce all eight experiment cases or the paper's reported test results. The paper results documented here are transcribed from the publication, not independently reproduced.

## Project contents

- DICOM loading with `pydicom`
- Slice ordering using `ImagePositionPatient[0]`
- Background cropping, 512 × 512 zero-padding, and per-slice min-max normalization
- 2D U-Net encoder-decoder with skip connections
- Five notebook training configurations
- Validation predictions and MSE, MAE, and PSNR calculations

## Notebook model and experiments

Input shape: `(512, 512, 1)`. Encoder widths: 32, 64, 128, and 256 channels; bottleneck: 512 channels. The decoder uses transposed convolutions and skip connections. Models use Adam, mean squared error loss, and mean absolute error as a metric.

| Variant | Output activation | Validation fraction | Epoch limit | Batch size | Early stopping |
|---|---|---:|---:|---:|---|
| 1 | ReLU | 0.20 | 30 | 4 | No |
| 2 | Sigmoid | 0.20 | 150 | 4 | Yes |
| 3 | ReLU | 0.20 | 150 | 4 | Yes |
| 4 | ReLU | 0.20 | 150 | 4 | Yes |
| 5 | ReLU | 0.30 | 50 | 4 | Yes |

Variants 2–5 monitor training `loss` with patience 5. The split uses `random_state=2).

## Data and privacy

The clinical dataset is not included. Update the machine-specific Windows paths in the notebook before running it. Verify T1/FLAIR subject and slice correspondence: the modalities are sorted independently.

Do not commit patient DICOM files, identifiable medical images, or restricted metadata. Follow institutional ethics, consent, data-use, and information-security requirements.

## Setup

```bash
python -m venv .venv
# Activate the environment, then:
python -m pip install --upgrade pip
pip install -r requirements.txt
jupyter notebook
```

Open the notebook, update data paths, and execute cells in order. Exact original package versions were not recorded; pin tested versions for reproducibility.

## Important implementation caveats

1. Preprocessing scales images to 0–1, but the model also divides inputs by 255.
2. Independently sorted modalities require explicit pairing verification.
3. The split is slice-level, not patient-level; this can cause patient leakage.
4. Early stopping monitors training loss rather than validation loss.
5. All-zero/constant slices and crops larger than 512 × 512 need robust handling.
6. Cropping may omit the final non-zero row and column.
7. Variant 2 uses sigmoid output; other variants use ReLU.
8. No separate held-out test cohort or external validation is defined.

Do not silently change these details and report the result as the original experiment. Document corrections as a new version or experiment.

## Documentation

- [Paper study: methods, dataset, and published results](docs/PAPER_STUDY.md)
- [Notebook methodology and implementation caveats](docs/METHODOLOGY.md)
- [Notebook experiment configurations and reporting template](docs/EXPERIMENTS.md)
- [Model architecture](docs/MODEL_ARCHITECTURE.md)
- [Results reporting template](results/RESULTS_TEMPLATE.md)

## Notebook file

The notebook is stored as a gzip-compressed Jupyter file at [`notebooks/T1toFlairUnet40slice.ipynb.gz`](notebooks/T1toFlairUnet40slice.ipynb.gz) to keep the repository upload compact. Download it and decompress it to `T1toFlairUnet40slice.ipynb` before opening in Jupyter. Notebook code and markdown are preserved; saved cell outputs and execution counts were cleared to avoid publishing embedded MRI previews and stale run output.

## Publication

Patel, P., Kamboj, P., & Nanavati, N. (2026). “Inter-modality MRI Image Translation using Deep Learning.” *Grenze International Journal of Engineering and Technology*, January issue, 1281–1295.

The source PDF is not included. Confirm publication and redistribution rights before adding the full paper or figures.
