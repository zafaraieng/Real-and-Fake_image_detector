# Real vs AI-Generated Image Detector

Independent research project that trains a lightweight **MobileNetV2** classifier to distinguish real photographs from AI-generated images using the **CIFake** dataset.

> Undergraduate research prototype focused on efficiency, reproducibility, and clear experimental reporting.

---

## Motivation

The rapid improvement of generative models has made synthetic images increasingly difficult to detect by eye. Lightweight, deployable detectors are useful for content moderation, media forensics, and educational tools. This project explores whether a compact CNN (MobileNetV2) can achieve strong performance on a standard real/fake benchmark while remaining practical on modest hardware.

## Problem Statement

Given an RGB image, classify it as **REAL** or **FAKE** (AI-generated). The system should be:

- Accurate on a held-out test set
- Lightweight enough for CPU inference
- Reproducible (fixed seeds, documented splits and hyperparameters)

## Research Objectives

1. Train a MobileNetV2-based binary classifier on the CIFake dataset.
2. Evaluate on the official held-out test split (20 000 images).
3. Document training methodology, hyperparameters, and limitations.
4. Provide a minimal interactive demo for qualitative inspection.

## Dataset

**CIFake** (Real and AI-Generated Synthetic Images)  
Source: [Kaggle – birdy654/cifake-real-and-ai-generated-synthetic-images](https://www.kaggle.com/datasets/birdy654/cifake-real-and-ai-generated-synthetic-images)

| Split | REAL | FAKE | Total |
|-------|------|------|-------|
| Train | 50 000 | 50 000 | 100 000 |
| Test  | 10 000 | 10 000 | 20 000  |

Images are resized to **96 × 96** for the lightweight training regime used in this work. The official test split is reserved for final evaluation. The notebook creates a deterministic 10% validation holdout from the official training split for checkpoint and threshold selection.

**Important:** Do not commit the dataset or Kaggle credentials. Download the dataset yourself using a personal Kaggle API token (never hard-code keys).

## Model Architecture

- Backbone: MobileNetV2 (ImageNet pretrained weights via `timm`)
- Input resolution: 96 × 96
- Classification head: binary (REAL / FAKE)
- Training strategy: two-phase transfer learning (freeze backbone → fine-tune)
- Optional test-time augmentation (TTA) mentioned in the experimental notebook

```mermaid
flowchart LR
    A[Input Image<br/>96×96] --> B[MobileNetV2 Backbone]
    B --> C[Global Pooling]
    C --> D[Binary Classifier]
    D --> E[REAL / FAKE + confidence]
```

## Experimental Setup (from notebook)

| Setting              | Value                          |
|----------------------|--------------------------------|
| Framework            | PyTorch                        |
| Backbone             | MobileNetV2 (timm)             |
| Image size           | 96 × 96                        |
| Batch size           | 32                             |
| Epochs               | Phase 1 = 3, Phase 2 = 8       |
| Random seed          | 42                             |
| Device               | CUDA when available            |
| Augmentations        | Albumentations pipeline        |

The notebook uses a deterministic 90/10 split of the official training data for training and validation. The official 20,000-image test split is used only after checkpoint selection. Threshold selection is performed on validation data; test metrics use the selected threshold or a fixed 0.5 threshold as explicitly labeled.

## Results Status

**No verified held-out test metrics are currently published.** Earlier notebook runs selected checkpoints and tuned thresholds against the official test split, so those reported figures are not independent held-out estimates and should not be cited as unbiased results. The revised notebook now reserves the official test split for final evaluation, but it must be rerun from a clean runtime before any new metric is reported.

A proper results table, confusion matrix, ROC curve, and ablation notes will be added once verified experimental artifacts are available.

## Limitations

- The revised notebook separates training, validation, and official held-out test data. A fresh end-to-end run is still required to produce valid final metrics. External dataset generalization has not yet been reported.
- 96 × 96 resolution discards fine-grained detail that larger models may exploit.
- No adversarial robustness or out-of-distribution analysis is included in the present version.
- Model weights and full experiment logs are not yet packaged for one-click reproduction.

## Future Work

- Publish verified metrics with confidence intervals.
- External dataset evaluation (e.g., other synthetic-image benchmarks).
- Ablations on Mixup, TTA, and resolution.
- Model card with intended use, risks, and out-of-scope applications.
- Lightweight export (ONNX / TorchScript) and measured CPU/GPU latency.

## Installation & Reproduction

```bash
# Clone
git clone https://github.com/zafaraieng/Real-and-Fake_image_detector.git
cd Real-and-Fake_image_detector

# Python environment (recommended)
python -m venv .venv
source .venv/bin/activate   # or Windows equivalent
pip install -r requirements.txt   # to be added

# Kaggle credentials (never commit these)
# The notebook prompts you to upload kaggle.json into the runtime

# Open the research notebook
jupyter notebook notebooks/Real\&Fake_image_detector.ipynb
```

Demo UI (optional):

```bash
npm install
npm run dev
```

## Project Structure

```
├── notebooks/                  # Research notebook(s)
├── src/                        # Demo frontend (Vite + React)
├── README.md
├── .gitignore
└── package.json                # Demo UI only
```

Further separation into `src/`, `scripts/`, `configs/`, and `tests/` is planned.

## Security Note

Never commit API keys, Kaggle tokens, or `.env` files.  
Rotate any key that has ever appeared in a public commit or notebook cell.

## License

To be determined after reviewing dataset and dependency licenses.  
Code is currently provided for research and educational purposes.

## Citation

If you use this work, please cite the repository and the original CIFake dataset authors.

---

*Undergraduate independent research project. Feedback and collaboration welcome.*
