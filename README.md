# Network Protocol Classification Using Machine Learning

Predict the network **protocol** of a packet (GQUIC, UDP, QUIC, TCP, DNS, etc.) from packet information using four Scikit-learn classifiers.

> Academic Machine Learning project by **Supriya Das**.

## Overview

The goal of this project is to predict the `Protocol` of a captured network packet using Machine Learning.

The project follows a complete Machine Learning workflow:

**Load Data → Understand → Clean → Feature Engineering → EDA → Feature Selection → Target Encoding → Train/Test Split → Model Training → Evaluation → Comparison → Prediction**

## Dataset

The dataset was prepared by combining five separate CSV files containing network traffic information.

**Original CSV files:**

- `YouTube_Live_1.csv`
- `YouTube_Live_2.csv`
- `YouTube_Live_3.csv`
- `YouTube_Live_4.csv`
- `YouTube_Live_5.csv`

These five files were merged into a single dataset named:

`merged_dataset.csv`

The merged dataset was uploaded to Google Drive and used for the Machine Learning experiments in Google Colab.

**Dataset information:**

- **Original dataset size:** 6,263,188 packets
- **Columns:** 7
- **Original protocols:** 14
- **Sample used:** Approximately 100,000 packets. All packets of rare protocols were retained, while common protocols were sampled proportionally.

**Dataset columns:**

- `No.`
- `Time`
- `Source`
- `Destination`
- `Protocol`
- `Length`
- `Info`

**Final classes (9):**

- GQUIC
- UDP
- QUIC
- TCP
- TLSv1.3
- DNS
- R-GOOSE
- DCP-PFT
- DCP-AF

Protocols with fewer than 50 samples were removed: THRIFT, TLSv1, MPEG TS, CLTP, and ar_drone.

The dataset is heavily imbalanced. GQUIC represents approximately 66% of the sample, while DCP-AF represents approximately 0.16%.

Therefore, balanced accuracy and macro F1-score are reported alongside ordinary accuracy.

> **Note:** The dataset is not included in this GitHub repository because of its size. The merged CSV file is stored separately in Google Drive. Update `DATA_PATH` in the notebook to point to your copy of the dataset.

## Methods

### 1. Data Cleaning

- Checked missing values.
- Checked duplicate packets.
- Missing values were found only in the `Info` column, which was not used for model training.
- Duplicate-like packets were counted but retained because identical packets can legitimately repeat in real network traffic.

### 2. Feature Engineering

Extracted the following features from the `Time` column:

- `Hour`
- `Minute`
- `Second`

### 3. Feature Sets Compared

Two feature sets were evaluated:

- **Length only:** Used as the main, more conservative experiment.
- **Length + Hour + Minute + Second:** Used to investigate the effect of time-based features.

### 4. Target Encoding

- Used `LabelEncoder` to convert protocol labels into numerical values.

### 5. Train/Test Split

- 80% training data
- 20% testing data
- Stratified split
- `random_state=42`

### 6. Feature Scaling

- Used `StandardScaler` inside a Scikit-learn pipeline.
- Scaling was fitted on training data only.

### 7. Machine Learning Models

Four classification algorithms were implemented:

1. Logistic Regression
2. K-Nearest Neighbors (KNN), k=5
3. Decision Tree, max_depth=10
4. Random Forest, 100 trees

### 8. Evaluation Metrics

- Accuracy
- Balanced Accuracy
- Macro F1-score
- Classification Report
- Confusion Matrix

## Results (Length Only)

The following table shows the performance of the four classifiers using packet length as the input feature.

| Model | Accuracy (%) | Balanced Accuracy (%) | Macro F1 (%) |
|---|---:|---:|---:|
| **KNN** | **79.35** | 42.58 | **45.16** |
| Random Forest | 74.58 | **47.89** | 39.89 |
| Decision Tree | 70.88 | 47.57 | 36.13 |
| Logistic Regression | 62.28 | 26.41 | 16.19 |

### Best Model

- **KNN:** Best in terms of accuracy and macro F1-score.
- **Random Forest:** Best in terms of balanced accuracy.

## Key Finding: The Time Shortcut

Adding `Hour`, `Minute`, and `Second` significantly increased Random Forest accuracy to **95.93%**.

However, this result is misleading because the time features allow the model to recognize the capture session rather than genuinely learn the network protocol.

The dataset contains the following time distribution:

- GQUIC packets appear only in captures from 2022-05-30 and 2022-05-31.
- Other protocols appear only in the 2022-06-28 capture.

A chronological test (training on the earliest 80% and testing on the latest 20%) confirms the problem:

- **Accuracy:** 59.73%
- **Balanced Accuracy:** 19.21%

The `Info` column was also excluded because it can almost directly reveal the protocol, which would introduce data leakage.

## Limitations

- DCP-AF, DCP-PFT, and R-GOOSE are rarely detected using packet length alone.
- The dataset has significant class imbalance.
- Balanced accuracy and macro F1-score are considerably lower than ordinary accuracy.
- The chronological test is challenging because GQUIC is absent from the test period.
- Time-based features can create misleadingly high performance when capture sessions are correlated with protocol labels.

## Future Work

- Include additional legitimate packet features, such as source and destination ports, TCP flags, and inter-arrival time.
- Collect more network traffic from different capture sessions.
- Apply class-imbalance techniques such as resampling.
- Evaluate models on independent network traffic captures.

## Installation and Execution

Install the required Python libraries:

```bash
pip install pandas numpy matplotlib seaborn scikit-learn jupyter
```

Launch Jupyter Notebook:

```bash
jupyter notebook
```

Alternatively, open the notebook directly in Google Colab.

Update `DATA_PATH` in the notebook configuration cell to point to the merged dataset stored on your computer or Google Drive before executing the notebook.

## Repository Structure

```text
ML-Project/
├── ML-Project.ipynb
└── README.md
```

## Tech Stack

- **Programming Language:** Python
- **Environment:** Google Colab / Jupyter Notebook
- **Data Processing:** Pandas, NumPy
- **Data Visualization:** Matplotlib, Seaborn
- **Machine Learning:** Scikit-learn

## Author

**Supriya Das**

Academic Machine Learning Project

---

*This project demonstrates the application of Machine Learning classification techniques to network traffic analysis, with particular attention to class imbalance, feature selection, data leakage, and model evaluation.*
