# NEO-Bench

NEO-Bench brings together five datasets for detecting asteroids and other streak-like objects in astronomical images. The datasets share a consistent YOLO bounding-box format, making it easier to reproduce experiments, train on data from several sources, test on an unseen source, and compare machine-learning models with classical detection methods.

## Included Datasets

| Dataset | Training images | Validation images | Test images | Total images | Positive images | Negative images | Boxes |
|---|---:|---:|---:|---:|---:|---:|---:|
| Roboflow | 929 | 80 | 40 | 1,049 | 1,046 | 3 | 2,174 |
| Hubble Asteroid Hunter | 1,361 | 170 | 170 | 1,701 | 1,701 | 0 | 1,701 |
| StreakMind | 1,484 | 278 | 94 | 1,856 | 840 | 1,016 | 858 |
| StreaksYoloDataset | 1,722 | 333 | 333 | 2,388 | 1,642 | 746 | 1,642 |
| UAEMMN 10% (seed 42) | 1,103 | 139 | 140 | 1,382 | 740 | 642 | 740 |
| **Total** | **6,599** | **1,000** | **777** | **8,376** | **5,969** | **2,407** | **7,115** |

Each dataset includes a `data.yaml` configuration file and uses the following folder structure:

```text
datasets/<dataset>/
├── data.yaml
├── train/
│   ├── images/
│   └── labels/
├── validation/
│   ├── images/
│   └── labels/
└── test/
    ├── images/
    └── labels/
```

All labeled objects use class `0` (`streak`). An image without a matching label file is a valid negative example, meaning that it contains no labeled streak.

## Dataset Folders

- `datasets/roboflow_YOLO`
- `datasets/HubbleAsteroidHunter_YOLO`
- `datasets/StreakMind_YOLO`
- `datasets/StreaksYoloDataset_YOLO`
- `datasets/UAEMMN_YOLO`

The published StreakMind split does not include a dedicated validation set. NEO-Bench therefore uses a fixed 278-image validation partition derived from the original training data, leaving 1,484 training images and preserving the original 94-image test set. Reuse this partition in every experiment.

## UAEMMN Sample

The repository includes a 10% sample selected from the original 14,800-image UAEMMN collection with random seed 42. After removing 98 unsuitable negative images, it contains 1,382 images:

- 1,103 training images
- 139 validation images
- 140 test images

The original UAEMMN images did not include bounding boxes. Claude Opus 4.6 identified streaks and generated localized axis-aligned bounding boxes, which the authors manually verified. These labels are a contribution of NEO-Bench rather than labels supplied by the original UAEMMN archive. Their coordinates are normalized and stored in the same YOLO format as the other datasets. Because there were 98 images removed, there are some gaps in the names for photos used in UAEMMN.

## Labels and Conversions

The repository includes all images needed to use the converted datasets. When a standard YOLO label was missing but an oriented box was available, the smallest enclosing horizontal box was converted to standard YOLO format. Existing standard YOLO labels were left unchanged.

Each label row has the following form:

```text
class_id x_center y_center width height
```

The four box coordinates are stored as proportions of the image width and height, so their values range from 0 to 1.

## Downloading the Data

The image files are managed with Git LFS. Install Git LFS before cloning the repository or downloading its files:

```bash
git lfs install
git clone <repository-url>
cd NEOBench
git lfs pull
```

## Training

To train an Ultralytics model, provide the configuration file for the dataset you want to use. For example:

```bash
yolo detect train model=yolo26l.pt data="datasets/StreakMind_YOLO/data.yaml" epochs=20 seed=42
```

## Data Use

The five datasets come from different projects and may have different requirements for attribution, redistribution, or commercial use. Review the terms of each original source before sharing or using its data commercially.
