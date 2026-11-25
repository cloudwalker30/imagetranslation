# T1-to-FLAIR MRI Image Translation Using U-Net

**Academic research implementation**

This project explores paired 2D medical-image translation from T1-weighted (T1WI) MRI slices to corresponding FLAIR MRI slices using a U-Net-style convolutional neural network.

> **Research and educational use only.** This code is not a medical device. Translated images must not be used for diagnosis, treatment planning, or clinical decision-making.

## Project contents

- DICOM loading with `pydicom`
- Slice ordering using `ImagePositionPatient[0]`
- Background cropping, 512 × 512 zero-padding, and per-slice min-max normalization
- 2D U-Net encoder-decoder with skip connections
- Five training configurations
- Validation predictions and MSE, MAE, and PSNR calculations

## Model and experiments

Input shape: `(512, 512, 1)`. Encoder widths: 32, 64, 128, and 256 channels; bottleneck: 512 channels. The decoder uses transposed convolutions and skip connections. Models use Adam, mean squared error loss, and mean absolute error as a metric.

| Variant | Output activation | Validation fraction | Epoch limit | Batch size | Early stopping |
|---|---|---:|---:|---:|---|
| 1 | ReLU | 0.20 | 30 | 4 | No |
| 2 | Sigmoid | 0.20 | 150 | 4 | Yes |
| 3 | ReLU | 0.20 | 150 | 4 | Yes |
| 4 | ReLU | 0.20 | 150 | 4 | Yes |
| 5 | ReLU | 0.30 | 50 | 4 | Yes |

Variants 2–5 monitor training `loss` with patience 5. The split uses `random_state=2`.

## Data and privacy

The dataset is not included. Update the machine-specific Windows paths in the notebook before running it. Verify T1/FLAIR subject and slice correspondence: the modalities are sorted independently.

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

## Evaluation and reporting

Metrics are MSE, MAE, and PSNR (peak intensity 1.0). Record actual results, subject/slice counts, split protocol, software versions, hardware, and limitations. Do not publish illustrative or unverified metric values as results.

No author, institution, supervisor, publication, DOI, or license details were supplied. Add accurate attribution and an approved license after checking code ownership and institutional terms.
