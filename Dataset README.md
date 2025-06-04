# Dataset Documentation: MIAP Chest X-ray Dataset

## Overview
The MIAP (Medical Image Analysis Pipeline) dataset is a comprehensive collection of chest X-ray images designed for multi-label classification of thoracic pathologies. This dataset is hosted on Kaggle and serves as the foundation for training and evaluating deep learning models in medical image analysis.

## Dataset Access
- **Kaggle Link**: [MIAP Dataset](https://www.kaggle.com/datasets/gouravkai11/miap-dataset/data)
- **License**: CC BY-NC-SA 4.0 (Non-commercial use with attribution)
- **Size**: Approximately 5GB (compressed)

## Dataset Structure
The dataset is organized into three main splits with corresponding CSV files:

```
miap-dataset/
├── fixedTraindataset.csv
├── fixedVALdataset.csv
├── fixedTestdataset.csv
├── processed_train/          # Training images
│   ├── image1.png
│   ├── image2.png
│   └── ...
├── processed_val/            # Validation images
│   ├── image1.png
│   ├── image2.png
│   └── ...
└── processed_test/           # Test images
    ├── image1.png
    ├── image2.png
    └── ...
```

## CSV File Structure
Each CSV file contains the following columns:

1. **File Name**: Unique identifier for each image (e.g., "00009030_004.png")
2. **Combined Labels**: Pipe-separated list of pathology labels (e.g., "Atelectasis|Effusion")
3. **Other clinical metadata** (varies by specific dataset version)

## Image Characteristics
- **Format**: PNG
- **Dimensions**: Varying original sizes, processed to 224×224 for model input
- **Color**: Grayscale (stored as 3-channel PNGs for compatibility)
- **Preprocessing**: Images have been normalized and processed for consistency

## Pathology Labels
The dataset includes annotations for 15 thoracic pathologies:

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

(Note: "No Finding" is specifically excluded from analysis in the current pipeline)

## Dataset Statistics
| Split | Number of Images | Average Labels per Image |
|-------|------------------|--------------------------|
| Train | ~13,900 | 1.2 |
| Validation | ~4,700 | 1.2 |
| Test | ~4,700 | 1.2 |

Class distribution is imbalanced, with some pathologies being much more common than others.

## Recommended Usage
1. **Data Loading**: Use the provided `load_and_validate_data()` function to ensure all image paths are valid
2. **Class Balancing**: The pipeline automatically creates balanced datasets for each pathology
3. **Preprocessing**: Images are normalized using DenseNet-specific preprocessing

## Important Notes
1. The dataset contains real clinical data and should be used ethically
2. Some images may contain sensitive patient information (though anonymized)
3. The "COVID-19" class represents a smaller subset compared to other pathologies
4. The validation and test sets should not be used for training or hyperparameter tuning

## Citation
If you use this dataset in your research, please cite the original source (check Kaggle for current citation requirements). Example:

```
@misc{miap_dataset,
  author = {Gourav Kai},
  title = {MIAP Chest X-ray Dataset},
  year = {2023},
  publisher = {Kaggle},
  howpublished = {\url{https://www.kaggle.com/datasets/gouravkai11/miap-dataset/data}}
```

## Known Issues:
1. Some images may be corrupted or missing (the pipeline handles these automatically)
2. Label noise may be present (common in medical datasets)
3. Class imbalance requires special handling (addressed in the pipeline)

For questions about the dataset, please use the Kaggle discussion forum associated with the dataset page.
