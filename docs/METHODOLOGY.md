# Methodology

## Data preparation

The notebook loads T1WI and FLAIR DICOM slices from separate directories and sorts each modality independently using `ImagePositionPatient[0]`. Pixel arrays are extracted, zero-valued background is cropped, and each cropped image is centered on a 512 × 512 zero-filled canvas. Each slice is min-max normalized independently and converted to float32.

The notebook then creates training and validation arrays using `train_test_split` with `random_state=2`. Variants 1–4 use a 20% validation fraction; Variant 5 uses 30%.

## Model training

A 2D U-Net maps a single-channel T1 slice to a single-channel FLAIR prediction. The network uses an encoder-decoder structure with skip connections. Models are compiled with Adam, mean squared error loss, and mean absolute error metric.

## Evaluation

Validation predictions are compared with reference images using MSE, MAE, and PSNR. PSNR uses peak intensity 1.0. The notebook also displays validation/reference and predicted image pairs.

## Reproducibility and limitations

- The dataset is not part of this repository; local Windows paths must be updated.
- Independently sorting modalities does not guarantee correct paired slices. Verify subject, acquisition, orientation, and slice position.
- A slice-level split can place slices from the same patient in both training and validation sets. Prefer patient-level separation for independent evaluation.
- The notebook's model input Lambda divides by 255 even though preprocessing already normalizes to 0–1.
- Early stopping in Variants 2–5 monitors training loss, not validation loss.
- All-zero and constant-intensity images and crops larger than 512 × 512 require additional safeguards.
- The crop endpoint may omit the final non-zero row and column.
- No separate held-out test cohort or external validation is established.

Treat the notebook as a record of the supplied implementation. Document any corrections as a distinct experiment.
