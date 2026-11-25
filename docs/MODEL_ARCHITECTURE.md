# U-Net Model Architecture

The notebook implements a 2D U-Net-style encoder-decoder for paired MRI translation.

- Input: T1-weighted MRI slice, `(512, 512, 1)`
- Target: corresponding FLAIR MRI slice, `(512, 512, 1)`
- Output: one-channel predicted FLAIR image

## Encoder

The encoder stages use two 3 × 3 convolutions with ReLU activation and He normal initialization, with dropout and max pooling between stages. Feature widths are 32, 64, 128, and 256 channels.

## Bottleneck

The bottleneck uses 512 channels and dropout.

## Decoder

The decoder upsamples with 2 × 2 transposed convolutions, concatenates the corresponding encoder skip features, and applies convolutional blocks. The output layer produces one channel.

## Training configuration

Models use Adam, mean squared error loss, and mean absolute error as a metric. Variant 2 uses sigmoid output activation; the other listed variants use ReLU output activation.

## Input scaling note

The preprocessing code normalizes slices to 0–1, while the model also applies division by 255. Review this double scaling before interpreting or reproducing results.
