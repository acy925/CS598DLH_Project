# CS598DLH_Project
This repository is for UIUC CS598DLH Project

## Structure

The content of this repository can be divided into two parts:

- Tools for build benchmark dataset to get the in-hospital-mortality data.
- The reproduced model of Concare .

## Requirements

For the data pre-processing, the following package will be used:

- pandas==0.23.4
- pyyaml==5.4.1
- numpy==1.16.5

For the ConCare model, the train and test on the Google Colab.

## Building the in-hospital-mortality data set

The MIMIC-III data is from https://physionet.org/

The source code is from https://github.com/YerevaNN/mimic3-benchmarks/, below are the steps.

1. Clone the repo.

   ```
   git clone https://github.com/YerevaNN/mimic3-benchmarks/
   cd mimic3-benchmarks/
   ```

2. This command will generates one directory per patient and writes ICU stay information to *data/{SUBJECT_ID}/stays.csv*, diagnose to *data/{SUBJECT_ID}/diagnoses.csv*, and events to *data/{SUBJECT_ID}/events.csv*.

   There will be 33,798 patients, 42,276 ICU stays and 253,116,883 events.

   ```
   python -m mimic3benchmark.scripts.extract_subjects {PATH TO MIMIC-III CSVs} data/root/
   ```

3. This command fix some issues (ICU stay ID is missing) and removes the events that have missing information. 

   There will be 208,572,237 events left, the number of patients and ICU stays remain the same.

   ```
   python -m mimic3benchmark.scripts.validate_events data/root/
   ```

4. The next command breaks up per-subject data into separate episodes (pertaining to ICU stays). Time series of events are stored in *{SUBJECT_ID}/episode{#}_timeseries.csv* (where # counts distinct episodes) while episode-level information (patient age, gender, ethnicity, height, weight) and outcomes (mortality, length of stay, diagnoses) are stores in *{SUBJECT_ID}/episode{#}.csv*. 

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

   And you will get three csv files, which is test_listfile.csv, train_listfile.csv, and val_listfile.csv. Finally, There will be 3237 samples in the test set, 3223 samples in validation set, and 14682 samples in training set.

   ```
   python -m mimic3models.split_train_val {dataset-directory}
   ```

After getting the in-hospital mortality dataset, save the files in `in-hospital-mortality` directory to `data/` directory.

## Train and test ConCare

The source code is from https://github.com/Accountable-Machine-Intelligence/ConCare.

For running the ConCare model,  I run it on the Google Colab.
