# Medical Image Analysis Pipeline (MIAP) Documentation

## Overview
This documentation describes a deep learning pipeline for multi-label classification of chest X-ray images using DenseNet121 architecture. The system is designed to detect 15 different thoracic pathologies from chest radiographs, with additional capabilities for model interpretability through Grad-CAM visualizations.

## Key Features
- Multi-label classification for 15 thoracic pathologies
- DenseNet121-based architecture with custom classification heads
- Class-balanced training approach
- Grad-CAM visualization for model interpretability
- Comprehensive evaluation metrics
- Automated threshold optimization

## System Requirements
- Python 3.7+
- TensorFlow 2.x
- CUDA-enabled GPU (recommended)
- Standard Python data science stack (NumPy, Pandas, scikit-learn)

## Dataset Structure
The pipeline expects the following directory structure:
```
/kaggle/input/miap-dataset/
    ├── fixedTraindataset.csv
    ├── fixedVALdataset.csv
    ├── fixedTestdataset.csv
    ├── processed_train/      # Training images
    ├── processed_val/        # Validation images
    └── processed_test/       # Test images
```

## Configuration Parameters
The system is configured through the following constants at the beginning of the script:

| Parameter | Description | Default Value |
|-----------|-------------|---------------|
| `TRAIN_CSV` | Path to training CSV file | "/kaggle/input/miap-dataset/fixedTraindataset.csv" |
| `VAL_CSV` | Path to validation CSV file | "/kaggle/input/miap-dataset/fixedVALdataset.csv" |
| `TEST_CSV` | Path to test CSV file | "/kaggle/input/miap-dataset/fixedTestdataset.csv" |
| `IMAGE_DIRS` | Dictionary of image directories for train/val/test | Paths to processed image directories |
| `CLASSES_TO_EXCLUDE` | Classes to exclude from analysis | ["No Finding"] |
| `IMAGE_SIZE` | Input image dimensions | (224, 224) |
| `BATCH_SIZE` | Training batch size | 32 |
| `EPOCHS` | Maximum training epochs | 30 |
| `GRADCAM_SAMPLES` | Number of Grad-CAM samples to generate | 5 |
| `LEARNING_RATE` | Initial learning rate | 2e-4 |
| `DROPOUT_RATE` | Dropout rate in model | 0.6 |

## Classes Detected
The system is trained to detect the following 15 thoracic pathologies:
1. Atelectasis
2. COVID-19
3. Cardiomegaly
4. Consolidation
5. Edema
6. Effusion
7. Emphysema
8. Fibrosis
9. Hernia
10. Infiltration
11. Mass
12. Nodule
13. Pleural_Thickening
14. Pneumonia
15. Pneumothorax

## Pipeline Components

### 1. Data Loading and Validation
- `load_and_validate_data()`: Loads and validates all image paths from CSV files
  - Checks for multiple image extensions (png, jpg, jpeg)
  - Processes labels into binary format using MultiLabelBinarizer
  - Excludes specified classes (e.g., "No Finding")

### 2. Dataset Creation
- `create_class_dataset()`: Creates balanced datasets for each pathology
  - Ensures equal positive and negative samples
  - Applies image augmentation (random flips, brightness/contrast adjustments)
  - Uses TensorFlow Data API for efficient loading

### 3. Model Architecture
- `DenseNetWithHead`: Custom model class extending DenseNet121
  - Base DenseNet121 with frozen initial layers
  - Custom head with GlobalAveragePooling, Dropout, and Sigmoid output
  - Fine-tunes last 20 non-BatchNorm layers
  - Uses Binary Focal Loss (gamma=2.0) to handle class imbalance

### 4. Training Process
- `train_single_class()`: Trains a model for a specific pathology
  - Implements class weighting based on positive sample frequency
  - Uses multiple callbacks:
    - EarlyStopping (patience=5, monitors val_auc)
    - ModelCheckpoint (saves best model)
    - ReduceLROnPlateau (reduces LR on plateau)
    - CSVLogger (saves training history)
  - Optimizes decision threshold based on validation F1 score

### 5. Model Interpretation
- `generate_gradcam()`: Generates Grad-CAM heatmaps
  - Visualizes model attention on conv5_block16_2_conv layer
  - Highlights regions contributing most to predictions
- `save_gradcam_images()`: Saves Grad-CAM visualizations
  - Overlays heatmaps on original images
  - Saves samples for qualitative evaluation

### 6. Evaluation
- `evaluate_models()`: Evaluates all trained models on test set
  - Loads optimal thresholds from training
  - Calculates micro/macro F1 and AUC scores
  - Generates test set predictions and Grad-CAM samples

## Output Files
The pipeline generates several output files in the working directory:

```
/kaggle/working/
    ├── models/                   # Saved Keras models (one per class)
    ├── gradcam/                  # Grad-CAM visualizations
    ├── logs/                     # Training history CSVs
    ├── thresholds.json           # Optimal thresholds per class
    ├── predictions.csv           # Test set predictions
    └── test_metrics.json         # Aggregate test metrics
```

## Training Performance
The system achieved the following validation F1 scores:

| Pathology | Best F1 Score | Optimal Threshold |
|-----------|--------------|-------------------|
| Atelectasis | 0.724 | 0.650 |
| COVID-19 | 0.896 | 0.100 |
| Cardiomegaly | 0.794 | 0.750 |
| Consolidation | 0.726 | 0.750 |
| Edema | 0.821 | 0.800 |
| Effusion | 0.796 | 0.650 |
| Emphysema | 0.804 | 0.750 |
| Fibrosis | 0.729 | 0.750 |
| Hernia | 0.711 | 0.100 |
| Infiltration | 0.690 | 0.550 |
| Mass | 0.731 | 0.550 |
| Nodule | 0.683 | 0.600 |
| Pleural_Thickening | 0.705 | 0.700 |
| Pneumonia | 0.693 | 0.850 |
| Pneumothorax | 0.793 | 0.550 |

## Usage Notes
1. The pipeline trains separate models for each pathology
2. Class imbalance is handled through:
   - Balanced dataset creation (equal positive/negative samples)
   - Class weighting in loss function
   - Focal loss to focus on hard examples
3. For best results, ensure sufficient GPU memory is available
4. Training can be monitored through the CSV log files

## Future Improvements
1. Implement multi-task learning with shared feature extraction
2. Add ensemble methods to improve robustness
3. Incorporate patient metadata in classification
4. Develop a web interface for clinical deployment
5. Add DICOM support for direct PACS integration

This documentation provides a comprehensive overview of the medical image analysis pipeline. For implementation details, refer to the source code comments and the generated output files during execution.
