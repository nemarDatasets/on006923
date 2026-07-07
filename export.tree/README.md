# Dataset of Electroencephalograms of Juvenile Offenders

## Project's name

Desarrollo de un sistema inteligente multiparamétrico para el reconocimiento de patrones asociados a disfunciones neurocognitivas en jóvenes en conflicto con la ley en el departamento del Atlántico.

## Year of project execution

2021

## Authors and acknowledgment

Aura Polo, Elmer León-Becerra, Mariana Pino and Julie Viloria-Porto.

Ronald Ruiz for his assistance during the data collection process, and Sergio Miranda for his dedication to data processing and cleaning.

## Work team

* MAGMA Ingeniería research group
* Hogares Claret foundation

## Institutions

- Institución Universitaria de Barranquilla (sede Soledad)
- Universidad del Magdalena
- Universidad Autónoma del Caribe

## Description

This repository contains resting-state EEG data collected with the Biosemi ActiveTwo of 140 participants:
- 74 juvenile offenders (JO)
- 66 juvenile non-offender controls

Exclusion criteria: No psychiatric treatment, dental/orthodontic appliances.

Recruitment: JO Hogares Claret Foundation (Centro de Reeducación el Oasis & Fundación Luz de Esperanza).

Controls: Institución Nacional de Educación Media INEM Miguel Antonio Caro (Barranquilla).

## Contents of the dataset

### Core Files

- `dataset_description.json`: General information about the study
- `participants.json`: Demographic and group assignment data
- `participants.tsv`: Demographic and group assignment data in table format

### Features Data (EEG_JO_Dataset/code)

#### Feature file nomenclature

Files are named using the pattern:
`FR_Dats_band_{BAND}_EP_{EYESTATE}_{EPOCH#}_can_{CHANNEL}.xlsx`

| Component          | Example     | Description                                                               |
|--------------------|-------------|---------------------------------------------------------------------------|
| **FR_Dats_band**   | Fixed       | Prefix = "Feature Results Dataset"                                        |
| **{BAND}**         | `ALFA`      | EEG frequency band: `ALFA` = Alpha (8-13Hz); `BETA` = Beta (13-30Hz); `DELTA` = Delta (1-4Hz); `THETA` = Theta (4-8Hz)                                                               |
| **EP_{EYESTATE}_** | `EP_C_`     | Eye state during epoch: `C` = Eyes closed; `O` = Eyes open                |
| **{EPOCH#}**       | `1`         | Epoch number (1 or 2) two epochs per eye state                            |
| **can_**           | Fixed       | "Channel" prefix                                                          |
| **{CHANNEL}**      | `A1`        | Electrode position (ABCD system): First letter = A • B • C • D <br>- Number = Electrode ID (1-32)                                                                                   |

#### File Contents:

Each Excel file contains 7 features for the specified band/channel/epoch combination:

1. Mean Power
2. RMS of PSD
3. Standard Deviation
4. Min Power
5. Max Power
6. Skewness
7. Kurtosis

#### Examples:

1. `FR_Dats_band_ALFA_EP_C_1_can_A1.xlsx`
   - Alpha band features
   - First closed-eyes epoch
   - Channel A1 (Frontal electrode 1)

2. `FR_Dats_band_THETA_EP_O_2_can_C15.xlsx`
   - Theta band features
   - Second open-eyes epoch
   - Channel C15 (Posterior electrode 15)

3. `FR_Dats_band_BETA_EP_C_2_can_B7.xlsx`
   - Beta band features
   - Second closed-eyes epoch
   - Channel B7 (Central electrode 7)

#### Dataset Structure:

- 4 epochs per subject:
  - 2 closed-eyes: `EP_C_1`, `EP_C_2`
  - 2 open-eyes: `EP_O_1`, `EP_O_2`
- 128 channels (A1-D32)
- 4 frequency bands
- Total files per subject: 4 epochs × 128 channels × 4 bands = 2,048 files

### EEG Data

```
EEG_JO_Dataset/
├── code/
├── sub-{Subject ID}{Group}/
|   ├── eeg/
|   |   ├── sub-{Subject ID}{Group}_coordsystem.json
|   |   ├── sub-{Subject ID}{Group}_electrodes.tsv
|   |   ├── sub-{Subject ID}{Group}_task-{Task Name}_acq-{Datatype}_eeg.json # Epoched data sidecar json
|   |   ├── sub-{Subject ID}{Group}_task-{Task Name}_acq-{Datatype}_eeg.set # Epoched data
|   |   ├── sub-{Subject ID}{Group}_task-{Task Name}_channels.tsv
|   |   ├── sub-{Subject ID}{Group}_task-{Task Name}_desc-{Datatype}_eeg.json # Preprocessed data sidecar json
|   |   └── sub-{Subject ID}{Group}_task-{Task Name}_desc-{Datatype}_eeg.set # Preprocessed data
├── ...
├── CHANGES
├── dataset_description.json
├── participants.json
├── participants.tsv
└── README.md
```

#### File Nomenclature

| Denomination          | Value           | Description                                                      |
|-----------------------|-----------------|------------------------------------------------------------------|
| `sub-`                | Fixed      	    | Subject prefix                                                   |
| `{Subject ID}`        | Fixed           | **Unique identifier**:<br>- First digit = group (`1`=sg, `1`=sg2, `2`=cg) <br>- Last 3 digits = subject ID                                                                     |
| `{Group}`             | `cg`/`sg`/`sg2` | **Group**: `cg`=control, `sg`=study group 1, `sg2`=study group 2 |
| `{Task Name}`         | `restingstate`  | **Task name** (resting state)                                    |
| `acq-` `desc-`        | `acq-`/`desc-`  | **Label**: `acq-` = acquisition, `desc-` = description           |
| `{Datatype}`          | `epochs`/`preprocessed` | Adquisition type                                         |
| `eeg`                 | Electroencephalography data | Data type                                            |
| Extension             | `.set`          | **File type**: processed                                         |

#### Examples

1. `sub-1005sg_task-restingstate_acq-epochs_eeg.set` = Epochs EEG for **study group 1** subject 005 (full ID 1005)
2. `sub-1005sg_task-restingstate_desc-preprocessing_eeg.set` = Preprocessed EEG for **study group 1** subject 005 (full ID 1005)

## Methods

### EEG Acquisition

- **Device**: Biosemi ActiveTwo system
- **Electrodes**: 128 channels (radial placement, 10-20 system reference)
- **Additional channels**: EOG, ECG recorded
- **Sampling rate**: 2048 Hz (downsampled to 128 Hz during preprocessing)
- **Online filtering**: 0.1-100 Hz bandpass
- **Setup**:
  - Participants seated awake
  - Continuous monitoring for movements/sleep
  - Event markers via serial communication (paradigm triggers)

### Paradigms

*(Dataset contains only resting-state recordings)*

- **Resting State (RS)**:
  - Total duration: 12 minutes
  - Sequence:
    - 4 min alternating eyes closed/open (COCO: Closed-Open-Closed-Open)
    - 8 min eyes closed (excluded from current dataset)
- **Segment trimming**:
    - 5s post-event onset
    - 5s pre-event offset (to avoid transition artifacts)

### Preprocessing pipeline (EEGLAB/MATLAB)

1. **Visual inspection**:
   - Raw data review using BDFreader
   - Identification of bad channels/artifacts
2. **Downsampling**:
   - 2048 Hz → 128 Hz (resting-state data)
3. **Rereferencing**:
   - Average reference (replaced failed earlobe reference)
4. **Filtering**:
   - Bandpass FIR: 1-40 Hz
   - High-pass: 1 Hz (0.5 Hz cutoff, 425 points)
   - Low-pass: 40 Hz (45 Hz cutoff, 45 points)
5. **Artifact Removal**:
   - Bad channel rejection:
     - Flat signals > 5s
     - SD > 4
     - Correlation < 0.8 with neighbors
   - ASR (Artifact Subspace Reconstruction)
   - ICA + ICLabel (components >90% non-brain removed)

### Feature Extraction

- **PSD Calculation**: Welch's method (50% overlap, Hamming window)
- **Frequency bands**:
  - Delta (δ): 1-4 Hz
  - Theta (θ): 4-8 Hz
  - Alpha (α): 8-13 Hz
  - Beta (β): 13-30 Hz
- **Features per band/channel**:
  1. Mean Power
  2. RMS of PSD
  3. Standard Deviation
  4. Minimum Power
  5. Maximum Power
  6. Skewness
  7. Kurtosis
- **Feature volume**: 14,336 features/subject (4 bands × 128 channels × 4 segments × 7 features)

### Technical Specifications

- **Processing Hardware**:
  - Intel Core i5-9400F @2.9GHz
  - 16GB RAM
  - Windows 10 (64-bit)
- **Software**:
  - MATLAB 2020a
  - EEGLAB toolbox
  - Python (scikit-learn, pandas for feature selection)
- **Processing Time**: ~10 minutes/subject

## Funding

This research was funded by the SISTEMA GENERAL DE REGALÍAS - SGR and the MINISTERIO DE CIENCIA TECNOLOGÍA E INNOVACIÓN - MINCIENCIAS from Colombia, in the framework of the project “Desarrollo de un sistema inteligente multiparamétrico para el reconocimiento de patrones asociados a disfunciones neurocognitivas en jóvenes en conflicto con la ley en el departamento del Atlántico”, with grant number BPIN 2020000100006.

## Support

Correspondence: Aura Polo (apolol@unimagdalena.edu.co); Elmer León-Becerra (elmerleondb@unimagdalena.edu.co); Julie Viloria-Porto (julieviloriapp@unimagdalena.edu.co)