# WiFi Fingerprinting-Based Indoor Localisation

A self-directed machine learning project that predicts a device's building, floor and coordinates from WiFi signal strength (RSSI), using the public **UJIIndoorLoc** dataset.

## Dataset

- **Source:** [UJIIndoorLoc, UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/310/ujiindoorloc)
- **Training set:** 19,937 records; **official validation set:** 1,111 records
- **Features:** RSSI values from 520 WiFi access points (`WAP001`–`WAP520`)
- **Labels:** building (3 classes), floor (5 classes), space ID, and coordinates (`LONGITUDE`, `LATITUDE`, in metres)

The data files are not included in this repository. Download `trainingData.csv` and `validationData.csv` from the link above and place them in the same folder as the notebook.

## Methods

- Pre-processing: the value `100` (meaning "no signal detected") is mapped to `-105`, below the normal RSSI range
- Models: Random Forest, K-Nearest Neighbours and a multilayer perceptron (MLP, with standardised inputs)
- Tasks: building and floor classification, and coordinate regression
- Evaluation: all headline results are reported on the official validation set. Floor models were trained on 80% of the training data (the remaining 20% formed the random-split comparison); building and coordinate models were trained on the full training set

## Key Results (official validation set)

| Task | Best model | Result |
|---|---|---|
| Building classification | Random Forest | 99.9% accuracy |
| Floor classification | Random Forest | 90.5% accuracy |
| Coordinate regression | KNN | median error 5.35 m; 73.7% of points within 10 m |

## Main Findings

1. **Random splitting gives much higher accuracy than the independent validation set.** For floor prediction, a random 80/20 split of the training data gave 99.6% accuracy, compared with 90.5% on the official validation set. This is likely related to repeated measurements at the same locations and spatial correlation, although differences in collection time may also contribute.
2. **Room classification could not be evaluated reliably.** The official validation set provides no room labels, and `SPACEID` is reused across buildings and floors (123 IDs correspond to 735 distinct rooms). Under a location-grouped split, 63.2% of validation records belonged to rooms never seen in training, so room accuracy was not a suitable measure of localisation at new locations.
3. **Coordinate regression was used instead.** It allows evaluation in metres on the official validation set. However, most validation points lie close to training locations (median distance 0.80 m), so the results mainly reflect performance near previously sampled areas rather than at completely new locations.

## Limitations

- The location-grouped split uses 1-metre coordinate bins and is not a strict spatial hold-out test.
- Evidence on more distant locations is exploratory (126 validation points more than 3 m from any training point).
- Hyperparameter experiments were limited, and the MLP was not extensively tuned.

## How to Run

1. Install the dependencies:
   ```
   pip install pandas numpy scikit-learn matplotlib jupyter
   ```
2. Place `trainingData.csv` and `validationData.csv` in the project folder.
3. Open either notebook in Jupyter Notebook and run all cells. The 735-class room experiments in both notebooks need a fair amount of memory; close other programs before running them.

## Files

- `wifi_v1_exploration.ipynb` – early exploration, kept as a record of how the project developed: data inspection, floor prediction and the random-split vs. official-validation comparison, confusion matrix, room prediction with parameter experiments, KNN and MLP comparisons, and the first location-grouped split and room-label checks
- `wifi_v2_clean.ipynb` – cleaned-up version: pre-processing, building prediction, coordinate regression, and the evaluation checks (class coverage under location-grouped splitting, distance from validation points to training points)
- `WiFi_Indoor_Localization_Report.docx` – full project report (in Chinese)
