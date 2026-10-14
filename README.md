# RF-DETR for Eyelid Tumour Detection

Author: Salini S  
Period: July–August 2026  
Project:Smartphone Imaging-Based AI for Early Detection and Screening of Ocular Surface and Eyelid Tumours

## Overview

This folder contains RF-DETR Medium experiments for localising eyelid tumours with bounding boxes. It includes training with tumour annotations alone, an experimental variant that incorporates normal images as negative samples, and inference on an external test set.

## Included notebooks

| Notebook | Purpose |
|---|---|
| `RF-DETR.ipynb` | Trains the single-class RF-DETR Medium model on annotated eyelid-tumour images. |
| `RF-DETR_withNegatives.ipynb` | Trains an RF-DETR Medium variant with normal images included as negative samples and demonstrates resuming training from a checkpoint. |
| `RF-DETR_Test.ipynb` | Loads the selected single-class EMA checkpoint, performs external inference, draws bounding boxes, and exports image-level predictions. |

## Model and training configuration

- Model: RF-DETR Medium (`RFDETRMedium`)
- Detection target: eyelid tumour
- Annotation format: COCO
- Training epochs: 20
- Batch size: 4
- Gradient accumulation steps: 4
- Learning rate: `1e-4`
- Training environment: Google Colab with GPU
- Checkpoint selection: best exponential moving average (EMA) checkpoint

## Experimental variants

### Single-class training

The single-class model was trained using annotated eyelid-tumour images. This model was selected for the external inference notebook because preserving tumour sensitivity was the priority for screening.

### Training with normal-image negatives

The negative-image variant included normal images without tumour bounding boxes. Its purpose was to investigate whether explicit background examples could reduce false-positive detections. The best EMA validation mAP@0.50:0.95 was **0.4476**, higher than the value recorded in the single-class training log.

On the small external evaluation set, however, this variant missed one tumour image. The experiment therefore demonstrated a trade-off: improved validation mAP did not translate into improved external screening sensitivity.

## External evaluation

The external set contained 32 images: 23 eyelid-tumour images and 9 normal images. The following image-level results were recorded in the project comparison.

| Model | Sensitivity | Specificity | Precision | F1 score | Accuracy |
|---|---:|---:|---:|---:|---:|
| **RF-DETR single-class** | **100.00%** | 88.89% | **95.83%** | **97.87%** | **96.88%** |
| RF-DETR with negatives | 95.65% | 88.89% | 95.65% | 95.65% | 93.75% |

Corresponding image-level confusion-matrix counts:

| Model | TP | FN | TN | FP |
|---|---:|---:|---:|---:|
| RF-DETR single-class | 23 | 0 | 8 | 1 |
| RF-DETR with negatives | 22 | 1 | 8 | 1 |

The single-class model was retained as the preferred screening candidate because it detected all tumour-positive images in this external set. These classification-style metrics indicate whether an image contained at least one qualifying detection; they do not measure bounding-box localisation quality. Localisation performance is represented separately by detection metrics such as mAP.

## Dataset and credentials

The image datasets, trained checkpoints, and API credentials are not included in this folder. The training notebooks request the Roboflow API key securely with `getpass`. Do not replace this with a hard-coded key.

Update the following paths and authorized dataset configuration before execution:

- Roboflow workspace, project, and dataset version
- Google Drive output directory
- Trained checkpoint path
- External test-image directory
- Prediction-output directory

## Requirements

The principal packages are:

```bash
pip install "rfdetr[train]" roboflow supervision
```

## Limitation

The external evaluation set is small, so the results should be treated as preliminary rather than clinically validated performance.