# Methodology

## Repository notebook implementation

The notebook loads T1WI and FLAIR DICOM slices from separate directories and sorts each modality independently using `ImagePositionPatient[0]`. Pixel arrays are extracted, zero-valued background is cropped, and each cropped image is centered on a 512 × 512 zero-filled canvas. Each slice is min-max normalized independently and converted to float32.

The notebook then creates training and validation arrays using `train_test_split` with `random_state=2`. Variants 1–4 use a 20% validation fraction; Variant 5 uses 30%.

A 2D U-Net maps a single-channel T1 slice to a single-channel FLAIR prediction. The network uses an encoder-decoder structure with skip connections. Models are compiled with Adam, mean squared error loss, and mean absolute error metric.

Validation predictions are compared with reference images using MSE, MAE, and PSNR. PSNR uses peak intensity 1.0.

## Related published study

The paper **“Inter-modality MRI Image Translation using Deep Learning”** by Patel, Kamboj, and Nanavati (2026) reports a broader supervised DICOM translation study with both T1WI → FLAIR and T2WI → FLAIR tasks. Its preprocessing includes DICOM cleaning, matching paired image dimensions with SimpleITK, sorting slices by `InstanceNumber`, converting pixel arrays with pydicom, cropping and zero-padding to modality-specific square dimensions, and min-max normalization to 0–1.

The paper reports 685 paired slices per modality after cleaning and a 342-slice subset. It describes an 80/10/10 train/validation/test split and compares a U-Net adapted from prior work with a proposed U-Net that changes dropout rates. The proposed model's dropout schedule is 0.1, 0.1, 0.3, 0.3, 0.5, 0.3, 0.3, 0.1, 0.1, compared with 0.1, 0.1, 0.2, 0.2, 0.3, 0.2, 0.2, 0.1, 0.1 in the referenced model.

See [PAPER_STUDY.md](PAPER_STUDY.md) for the complete paper-derived dataset description, experiment design, and reported result tables. These paper details should not be assumed to describe the notebook implementation below.

## Reproducibility and limitations

- The dataset is not part of this repository; local Windows paths must be updated.
- Independently sorting modalities does not guarantee correct paired slices. Verify subject, acquisition, orientation, and slice position.
- A slice-level split can place slices from the same patient in both training and validation sets. Prefer patient-level separation for independent evaluation.
- The notebook's model input Lambda divides by 255 even though preprocessing already normalizes to 0–1.
- Early stopping in Variants 2–5 monitors training loss, not validation loss.
- All-zero and constant-intensity images and crops larger than 512 × 512 require additional safeguards.
- The crop endpoint may omit the final non-zero row and column.
- No separate held-out test cohort or external validation is established in the notebook.

Treat the notebook as a record of the supplied implementation. Document any corrections as a distinct experiment.
