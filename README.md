# ESPBirdNet – Bird classification on edge devices from spectrograms

##### Internet of Things, Algorithms and Services - Sapienza University of Rome

---

### Authors:
> 1986191: Leonardo Mariut \
> 1986101: Martina Fortuna

---

This project explored running bird sound detection on low-power devices. The idea was to continuously sample audio, compute spectrograms directly on edge hardware, and run small neural networks for inference. We built it around the Heltec ESP32-S3 LoRa V3.2 board with a cheap INMP441 microphone, using Arduino IDE for firmware and TensorFlow Lite Micro for model execution.

The main challenge was hardware variability, microphones and spectrogram processing were inconsistent, which made trained models difficult to validate. The focus here is not polished accuracy but the whole IoT pipeline, from dataset preparation, to quantized models, to real-time inference under tight memory and power constraints.

---

## NeuroGreg

This repo handles the ML side. It provides scripts to fetch bird datasets from xeno-canto, extract mel spectrograms, and train small convolutional networks. Models are optimized with quantization aware training so they can fit in ESP32 memory and run inference with uint8 weights. Conversion tools export models as C headers ready to drop into microcontroller code. NeuroGreg is useful if you want to train new species classifiers or adapt the pipeline to other audio tasks.

[NeuroGreg repository](https://github.com/ESPBirdNet/NeuroGreg.git)

---

## MicroGreg

This is the firmware for the ESP32-S3 Heltec LoRa V3.2. It configures the INMP441 microphone, captures audio buffers, computes spectrograms directly on the board, and runs inference using TensorFlow Lite Micro. The goal was continuous on-device processing with low power use and no reliance on external servers. In practice it streams spectrogram data or predictions, while keeping memory and buffer sizes just within the limits of the hardware.

[MicroGreg repository](https://github.com/ESPBirdNet/MicroGreg.git)

---

## PicoGreg

This repo offloads visualization to a Raspberry Pi Pico. Acting as an I²C slave, the Pico receives spectrogram framebuffers from the ESP32 and renders them on an ST7789 display as a heatmap. It also signals status with the onboard LED. By pushing reception and rendering to the Pico, the ESP32 is left with more RAM for inference and DSP. Planned next steps included adding audio playback via microSD and I²S speakers, so we could run controlled tests of model predictions.

[PicoGreg repository](https://github.com/ESPBirdNet/PicoGreg.git)

---

## Further work

Further work should focus on using a higher quality digital microphone to reduce variability at the input stage, since most issues came from inconsistent spectrograms rather than the models themselves. Once a stable setup is in place, the next step would be to distill knowledge from larger networks into smaller ones, then compare and evaluate different model versions - distilled, quantized and full precision - both on the server and directly on edge devices, to measure trade-offs between accuracy, latency and resource use.
