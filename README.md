# Coverage Preserving Ensemble Framework with Minority Recovery for Robust Indoor Localization

This repository contains the research implementation for a BLE based indoor localization framework developed for the ABC 2026 Decode the Invisible Challenge. The goal is to predict caregiver room location in a nursing care facility while maintaining full prediction coverage and improving recognition of underrepresented rooms.

The method combines a Random Forest classifier, minority data recovery, temporal smoothing, adaptive confidence logic, ensemble voting, and a final minority boost stage. Unlike confidence filtering methods that leave some windows unpredicted, this framework is designed to produce a continuous room trace for every test window.

## Published Paper

Paper title: A Coverage Preserving Ensemble Framework with Minority Recovery for Robust Indoor Localization

Journal: International Journal of Activity and Behavior Computing

Year: 2026

DOI: https://doi.org/10.60401/ijabc.160

## Project Summary

Indoor localization in nursing care facilities is difficult because BLE signal patterns are noisy, rooms have unequal numbers of samples, and high traffic locations dominate the training data. Minority rooms may appear only a few times, causing normal classifiers to ignore them.

This project addresses that issue using a coverage preserving inference strategy. The pipeline processes BLE beacon RSSI data into fixed time windows, extracts interpretable features, trains a Random Forest model, and applies post processing modules that recover minority room predictions without leaving prediction gaps.

## Key Features

* BLE based room level localization for nursing care environments
* Processing of 4,107 BLE CSV files
* 40 second non overlapping window generation
* 33 dimensional feature representation
* Random Forest classifier with probability output
* Minority oversampling using Gaussian RSSI noise
* Viterbi based temporal regularization
* Dynamic adaptive thresholding
* Top K rescue for minority room recovery
* Ensemble voting with priority tie breaking
* Final minority boost for zero recall rooms
* Full prediction coverage with no rejected windows

## Dataset Overview

The dataset comes from the ABC 2026 Decode the Invisible Challenge. It contains BLE beacon records collected from a nursing care facility.

| Item | Value |
| --- | --- |
| BLE files | 4,107 |
| Fixed beacons | 25 |
| Window size | 40 seconds |
| Total windows | 4,250 |
| Labeled windows | 2,331 |
| Evaluated room classes | 22 |
| Feature count | 33 |
| Train test split | 70 percent training and 30 percent testing |
| Classifier | Random Forest |
| Random Forest trees | 500 |

## Methodology

The complete workflow follows these stages.

### 1. Raw BLE Data Processing

BLE CSV files are loaded and cleaned. RSSI measurements are aligned with timestamp information. The system uses stationary BLE beacon signals to represent room level movement patterns.

### 2. Temporal Windowing

The continuous BLE stream is divided into 40 second windows. Each window is treated as one localization instance. The midpoint of the window is used to align the window with the closest valid ground truth room label.

### 3. Feature Extraction

Each window is converted into a 33 dimensional feature vector.

| Feature group | Count | Description |
| --- | --- | --- |
| RSSI statistical features | 8 | Mean, variation, signal count, minimum, maximum, and related statistics |
| Beacon specific features | 25 | Mean RSSI value for each fixed beacon |
| Temporal features | 3 | Hour, minute, and microsecond based time features |

### 4. Minority Augmentation

To reduce severe class imbalance, minority room samples are replicated with additive Gaussian noise on beacon features. The noise is applied within a realistic RSSI range so the augmented samples preserve the original signal pattern while improving class representation.

### 5. Random Forest Training

A Random Forest classifier is trained using balanced class weighting. The model produces probability scores for all room classes, which are later used by the post processing and recovery modules.

### 6. Hybrid Temporal Smoothing

Viterbi based regularization is used to improve temporal consistency across consecutive room predictions. This helps reduce unstable jumps in ambiguous BLE signal regions.

### 7. Adaptive Thresholding

Class specific confidence logic is used to identify uncertain predictions. While thresholding can improve measured F1 score, it may reduce coverage because uncertain windows can be rejected. This project treats that limitation carefully and uses thresholding as one internal view rather than the final output policy.

### 8. Ultimate Inference Framework

The final inference framework combines multiple prediction views.

* Raw Random Forest prediction
* Viterbi smoothed prediction
* Adaptive threshold based prediction
* Local neighborhood consensus
* Top K minority rescue
* Ensemble voting
* Minority boost finalization

The final decision policy avoids rejection and assigns a room label to every test window.

## Results

| Strategy | Weighted F1 | Macro F1 | Coverage | Rooms detected |
| --- | --- | --- | --- | --- |
| Garcia baseline | 0.7432 | 0.5350 | 100 percent | 18 of 22 |
| Viterbi with adaptive threshold | 0.8225 | 0.6546 | 67.6 percent | 18 of 22 on retained windows |
| Ultimate proposed method | 0.7492 | 0.6241 | 100 percent | 20 of 22 |

The proposed method restores two difficult minority rooms.

| Room | Previous F1 | Ultimate F1 | Result |
| --- | --- | --- | --- |
| 503 | 0.000 | 0.800 | Recovered |
| 510 | 0.000 | 1.000 | Recovered |

## Main Contribution

The main contribution of this repository is a full coverage inference framework for imbalanced indoor localization. Instead of improving accuracy by removing difficult windows, the system keeps all windows and applies recovery logic to protect rare room classes.

This is useful for healthcare workflow analytics because missing prediction windows can break downstream analysis of caregiver movement, room occupancy, and activity flow.

## Repository Contents

```text
Coverage-preserving-localization/
├── README.md
├── Robust_Indoor_Localization_via_Minority_Recovery.pptx
└── (Final)_ABC2026_GARCIA_+_Hybrid_VITERBI_+_ADAPTIVE_THRESHOLD (2).ipynb

```
## Evaluation Metrics

The project reports the following metrics.

* Weighted F1 score
* Macro F1 score
* Per class precision
* Per class recall
* Per class F1 score
* Prediction coverage
* Number of detected rooms
* Missing room analysis

Weighted F1 reflects performance on frequent rooms. Macro F1 gives equal importance to every room and is therefore important for evaluating minority room recovery.

## Why Coverage Matters

In indoor localization for care facilities, a missing prediction is not only a model error. It creates a missing part in the movement trace. This can affect workflow analytics, room occupancy analysis, and activity documentation. The proposed method therefore treats coverage as a primary objective rather than a secondary metric.

## Limitations

* Some rooms have extremely low support, sometimes only one test example
* BLE RSSI values are sensitive to human body blocking, device orientation, and multipath interference
* Fixed 40 second windows may miss very short visits
* Probability calibration was not fully explored
* The method was evaluated on one challenge dataset and should be validated on more facilities

## Future Work

* Add probability calibration using Platt scaling or isotonic regression
* Test sliding and multi resolution windowing
* Add active learning for rare room sample collection
* Add drift monitoring for long term deployment
* Compare with deep sequence models
* Package the inference system as a reusable Python module

## Citation

If you use this work, please cite the published article.

```bibtex
@article{indoorLocalizationMinorityRecovery2026,
  title = {A Coverage Preserving Ensemble Framework with Minority Recovery for Robust Indoor Localization},
  author = {Khandaker, Sunzil and Shahriar, S.M. and Hasan, Md Mobashir and Adib, G.M.M Miftahul Alam and Hasan, Kazi Jahid and Arefin, Abu Shahed Shah Md Nazmul and Chowdhury, Md Tahmid and Uddin, Md Salah and Islam, Md Baharul},
  journal = {International Journal of Activity and Behavior Computing},
  volume = {2026},
  number = {2},
  pages = {1--17},
  year = {2026},
  doi = {10.60401/ijabc.160}
}
```

## License

This repository is intended for academic and research use. Please check the published paper and dataset source for reuse conditions before redistributing data.
