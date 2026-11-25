# Paper Study: Inter-modality MRI Image Translation Using Deep Learning

## Source and scope

This document summarizes the uploaded paper **“Inter-modality MRI Image Translation using Deep Learning”** by Prachee Patel, Dr. Pariza Kamboj, and Dr. Nirali Nanavati, published in *Grenze International Journal of Engineering and Technology*, January issue (2026), pp. 1281–1295.

The paper studies supervised synthesis of brain FLAIR MRI from either T1-weighted (T1WI) or T2-weighted (T2WI) DICOM images using U-Net-based models. It is broader than the notebook currently included in this repository: the notebook implements a T1WI-to-FLAIR workflow with five configurations, whereas the paper reports both T1WI-to-FLAIR and T2WI-to-FLAIR experiments.

## Motivation and objective

FLAIR acquisition takes longer than T1WI and T2WI in the imaging-centre workflow described by the paper. The authors investigate whether FLAIR images can be synthesized from the faster-acquired modalities, with the intended potential to reduce total scan time and motion artefacts. The paper reports acquisition-time ranges of:

| Sequence | Acquisition time |
|---|---:|
| T1WI | 1–4 minutes |
| T2WI | 1.5–4 minutes |
| FLAIR | 2–5 minutes |
| T1WI + T2WI + FLAIR | 4.5–13 minutes |

These are the ranges reported for the study's imaging facility, not universal MRI scan-time guarantees. Synthesized images are a research output and must not be treated as a substitute for clinically acquired sequences without appropriate clinical validation and regulatory oversight.

## Dataset

The paper describes brain MRI data supplied by Horizon Imaging Centre:

- 50 subjects initially, in DICOM format.
- Axial sequences for T1WI, T2WI, and FLAIR; the paper states that 20 slices per modality were available per subject.
- The dataset included scans from subjects with and without disease.
- Original image resolutions: T1WI 256 × 176, T2WI 384 × 300, and FLAIR 320 × 270.
- Five subjects were removed because a required modality file in DICOM format was unavailable.
- The paper then reports removing the last four slices per subject as unsuitable, plus two subjects and three slices due to blur/unclear image quality.
- After cleaning, the paper reports 685 slices per modality. It also evaluates a 342-slice dataset (described as 50% of the full dataset).

The paper's text contains an apparent typo in its list of paired datasets, repeating T1WI for the fourth item. Its experiment descriptions indicate the intended four pairings are T1WI–FLAIR and T2WI–FLAIR, each at 342 and 685 slices.

The source data are not included in this repository. Do not upload patient DICOM files or identifiable medical images.

## Preprocessing reported in the paper

1. **Data cleaning:** Exclude subjects/slices with missing DICOM modality files, unsuitable slices, blur, or unclear image quality, as described above.
2. **Match paired resolutions:** The paper uses SimpleITK functions so that each input/target pair has matching dimensions. It reports 256 × 176 for T1WI–FLAIR pairs and 300 × 384 for T2WI–FLAIR pairs before the later crop/pad step.
3. **Slice ordering and pairing:** DICOM slices are sorted using the DICOM `InstanceNumber` attribute to establish corresponding slices within a series.
4. **DICOM-to-array conversion:** Pixel data are extracted into NumPy arrays using the pydicom `pixel_array` property.
5. **Crop and zero-pad:** Background pixels are treated as zero; the brain region is cropped and placed centrally in a zero-filled square array. The reported final sizes are 256 × 256 for T1WI and its paired FLAIR images, and 480 × 480 for T2WI and its paired FLAIR images.
6. **Normalization:** Each image is min-max normalized to a range of 0–1.

The paper describes paired, supervised image-to-image translation: the source modality is the model input and the corresponding FLAIR image is the target.

## U-Net models

The study compares two U-Net-based models:

- A U-Net architecture adapted from the earlier work cited as reference [21].
- The authors' proposed U-Net, which modifies dropout rates to increase regularization, particularly in deeper layers.

The paper reports the following dropout rates, in the order used for its convolutional/deconvolutional blocks:

| Model | Dropout rates |
|---|---|
| U-Net from reference [21] | 0.1, 0.1, 0.2, 0.2, 0.3, 0.2, 0.2, 0.1, 0.1 |
| Proposed U-Net | 0.1, 0.1, 0.3, 0.3, 0.5, 0.3, 0.3, 0.1, 0.1 |

The architecture uses an encoder-decoder structure, with down-sampling in the encoder, transposed-convolution up-sampling in the decoder, and skip connections that concatenate corresponding encoder and decoder feature maps. The paper describes ReLU in intermediate layers and a sigmoid output in its general U-Net description. Consult the paper's architecture figures and implementation before reproducing layer-by-layer details.


## Experiment design

The paper describes two separately trained translation models:

- T1WI → FLAIR
- T2WI → FLAIR

Each translation direction is evaluated with the 685-slice dataset and the 342-slice dataset. Each dataset is split into 80% training, 10% validation, and 10% testing. This gives four dataset/direction combinations for each of the two model approaches (the proposed U-Net and the reference [21] U-Net), described as eight model-training/evaluation cases overall. The paper states that the reported comparison is at 150 epochs.

The evaluation uses unseen test images. The paper reports PSNR, MAE, and MSE between generated FLAIR images and ground-truth FLAIR images.

## Relationship to the repository notebook

The included notebook is a separate implementation record and should not be assumed to reproduce the paper's complete methodology or tables:

| Aspect | Paper | Repository notebook |
|---|---|---|
| Translation tasks | T1WI → FLAIR and T2WI → FLAIR | T1WI → FLAIR |
| Reported dataset sizes | 342 and 685 paired slices | Local dataset paths; notebook refers to 40-slice workflow in its filename |
| Split | 80/10/10 train/validation/test | Train/validation split; no separate held-out test set is defined |
| Image canvas | 256 × 256 (T1WI pairs), 480 × 480 (T2WI pairs) | 512 × 512 |
| Model comparison | Proposed U-Net and U-Net from [21] | Five configurations of the notebook's U-Net |
| Regularization | Paper reports dropout-rate changes | Do not assume the notebook matches the paper's dropout schedule |
| Evaluation metrics | PSNR, MAE, MSE on test data | MSE, MAE, PSNR on validation predictions |

For exact notebook implementation details and known limitations, see [Methodology](METHODOLOGY.md) and [Experiment Configurations](EXPERIMENTS.md). Do not label the paper's tables as notebook results.

## Limitations and reproducibility notes

- The repository does not contain the private clinical dataset, so the paper's experiments cannot be reproduced from this repository alone.
- The paper's 80/10/10 split is described, but the text does not clearly establish whether separation was performed at the subject level. A patient-level split should be explicitly documented to avoid leakage across slices from the same subject.
- The paper's reported metrics are aggregate table values; per-subject distributions, confidence intervals, and external-site validation are not provided in these tables.
- The reported results do not by themselves establish diagnostic equivalence, clinical safety, or readiness for clinical deployment.
- Some dataset-cleaning counts and the fourth pairing in the dataset list are worded ambiguously in the paper; this summary preserves the reported 685/342 totals and clarifies the apparent pairing typo using the experiment descriptions.

## Future direction stated by the authors

The paper proposes future work on generating FLAIR images from synthesized T2WI images using the DICOM dataset and the proposed U-Net model.

## Citation

Patel, P., Kamboj, P., & Nanavati, N. (2026). “Inter-modality MRI Image Translation using Deep Learning.” *Grenze International Journal of Engineering and Technology*, January issue, 1281–1295.

The source PDF is not redistributed in this repository. Add a DOI or publisher URL only after verifying the canonical publication record and redistribution terms.
