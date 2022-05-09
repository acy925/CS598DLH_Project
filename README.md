# CS598DLH_Project
This repository is for UIUC CS598DLH Project

## Citation


The reproduce project is based on the paper, "Ma, Liantao, et al. ”Concare: Personalized clinical feature embedding via capturing the healthcare context.” Proceedings of the AAAI Conference on Artificial Intelligence. Vol. 34. No. 01. 2020.


The link to this paper's repo is: https://github.com/Accountable-Machine-Intelligence/ConCare.


The data processing is based on the paper, "Harutyunyan, H.; Khachatrian,H.; Kale, D. C.; and Galstyan, A. 2017. Multitask learning and benchmarking with clinical time series data. arXiv preprint arXiv:1703.07771."


The link to this paper's repo is: https://github.com/YerevaNN/mimic3-benchmarks/.


## Structure

The content of this repository can be divided into two parts:

- Instructions for build benchmark dataset to get the in-hospital-mortality data, including the data download instructions and preprocessing codes.
- The reproduced model of ConCare, including the training and evaluation codes.
- The result of the project.

## Requirements

For the data pre-processing, the following package will be used:

- pandas==0.23.4
- pyyaml==5.4.1
- numpy==1.16.5

For the reproduced ConCare model, the train and test on the Google Colab.

## Instructions for build benchmark dataset to get the in-hospital-mortality data

The MIMIC-III data is down load from https://physionet.org/.

The demographic data is down load from https://github.com/Accountable-Machine-Intelligence/ConCare.

Below are the steps for preprocessing the In-hospital mortality dataset.

1. Clone the repo.

   ```
   git clone https://github.com/YerevaNN/mimic3-benchmarks/
   cd mimic3-benchmarks/
   ```

2. This command will generates one directory per patient and writes ICU stay information to `data/{SUBJECT_ID}/stays.csv`, diagnose to `data/{SUBJECT_ID}/diagnoses.csv`, and events to `data/{SUBJECT_ID}/events.csv`.

   There will be 33,798 patients, 42,276 ICU stays and 253,116,883 events.

   ```
   python -m mimic3benchmark.scripts.extract_subjects {PATH TO MIMIC-III CSVs} data/root/
   ```

3. This command fix some issues (ICU stay ID is missing) and removes the events that have missing information. 

   There will be 208,572,237 events left, the number of patients and ICU stays remain the same.

   ```
   python -m mimic3benchmark.scripts.validate_events data/root/
   ```

4. The next command breaks up per-subject data into separate episodes (pertaining to ICU stays). Time series of events are stored in `{SUBJECT_ID}/episode{#}_timeseries.csv` (where # counts distinct episodes) while episode-level information (patient age, gender, ethnicity, height, weight) and outcomes (mortality, length of stay, diagnoses) are stores in `{SUBJECT_ID}/episode{#}.csv`. 

   This script requires two files, one that maps event ITEMIDs to clinical variables and another that defines valid ranges for clinical variables (for detecting outliers, etc.). Outlier detection is disabled in the current version.

   There will be 31,868,114 events left, the number of patients and ICU stays remain the same.

   ```
   python -m mimic3benchmark.scripts.extract_episodes_from_subjects data/root/
   ```

5. This command splits the whole dataset into training and testing sets.

   There will be 5070 samples in test dictionary and 28728 samples in train dictionary.

   ```
   python -m mimic3benchmark.scripts.split_train_and_test data/root/
   ```

6. This command will generate the in-hospital mortality dataset.

   There will be 3237 samples in test dictionary and 17904 samples in train dictionary.

   ```
   python -m mimic3benchmark.scripts.create_in_hospital_mortality data/root/ data/in-hospital-mortality/
   ```

7. This command will generate the validation data set from the training set. 

   And you will get three csv files, which is `test_listfile.csv`, `train_listfile.csv`, and `val_listfile.csv`. Finally, There will be 3237 samples in the test set, 3223 samples in validation set, and 14682 samples in training set.

   ```
   python -m mimic3models.split_train_val {dataset-directory}
   ```

After getting the in-hospital mortality dataset, save the files in `in-hospital-mortality` directory to `data/` directory.

## Train and test ConCare


For training the ConCare model, in the `Concare` directory, run the `concare_notebook.ipynb` on the Google Colab.

For the baseline model of GRU, in the `Concare` directory, run the `concare_GRU_notebook.ipynb` on the Google Colab.

For the ablation experiments of GRU, in the `Concare` directory, run the `concare_ab_notebook.ipynb` on the Google Colab.

For the training the ConCare model for predicting the Length-of-stay, following the data preprocessing part above to get the  `length-of-stay` directory, then save the files in `length-of-stay` directory to `Concare_LOS/data/` directory, run the `concare_los_notebook.ipynb` on the Google Colab.


## The result of the reproduced model and original models

| Methods               | AUROC         | AUPRC         | min(Se, P+)   |
| --------------------- | ------------- | ------------- | ------------- |
| GRU                   | .8628(.011)    | .4989(.022)   | .5026(.028)   |
| CRETAIN               | .8313(.014)    | .4790(.020)   | .4721(.022)   |
| MCA-RNN               | .8587(.013)    | .5003(.028)   | .4932(.024)   |
| T-LSTM                | .8617(.014)    | .4964(.022)   | .4977(.029)   |
| Transformere          | .8535(.014)    | .4917(.022)   | .5000(.019)   |
| SAnD                  | .8382(.007)    | .4545(.018)   | .4845(.017)   |
| ConCare               | .8702(.008)    | .5317(.027)   | .5082(.021)   |
| ConCare PE            | .8566(.008)    | .4811(.024)   | .5012(.020)   |
| ConCare DE-           | .8671(.009)    | .5231(.028)   | .5080(.023)   |
| Reproduce GRU         | .8605(.009)    | .4928(.028)   | .4971(.027)   |
| Reproduce ConCare     | .8709(.009)    | .5310(.027)   | .5196(.022)   |
| Reproduce ConCare PE  | .8560(.009)    | .4805(.024)   | .5006(.024)   |
| Reproduce ConCare DE- | .8669(.009)    | .5228(.027)   | .5079(.022)   |
