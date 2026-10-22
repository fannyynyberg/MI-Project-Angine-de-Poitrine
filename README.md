# Identifying Musical Style from Audio Features
## A Case Study of Angine de Poitrine

This project investigates whether the unconventional musical style of **Angine de Poitrine (AdP)** can be identified from audio features alone, with particular focus on **rhythmic irregularity** and **microtonality**.

## Research Question

> Can the unusual musical style of Angine de Poitrine be identified from audio features alone, and which measurable characteristics, particularly rhythmic irregularity and microtonality, separate it from conventional rock?

Three hypotheses were tested:

- **H1 (Rhythm):** AdP exhibits greater rhythmic irregularity than GTZAN rock.
- **H2 (Pitch):** AdP deviates more from 12-tone equal temperament (12-TET).
- **H3 (Classification):** Rhythm and pitch features jointly separate AdP from GTZAN rock above a majority-class baseline.

## Data and Features

The dataset contains **196 30-second excerpts**:

- 96 excerpts from 12 AdP tracks
- 100 excerpts from the GTZAN rock category

Audio was converted to mono at 22.05 kHz and RMS-normalized before feature extraction.

A total of **76 features** were extracted:

| Feature family | Features |
|---|---:|
| Rhythm | 13 |
| Pitch / tonal | 25 |
| Timbre | 38 |

H1 uses the coefficient of variation of inter-onset intervals (`rhy_ioi_cv`) as the main measure of rhythmic irregularity.

H2 uses mean pitch deviation from the nearest 12-TET semitone (`pit_yin_dev_mean`) as the main measure of microtonality.

For statistical testing, clip-level values were aggregated to **source-track medians** to avoid treating multiple excerpts from the same AdP song as independent observations. H1 and H2 were evaluated with a two-sided **Mann-Whitney U test** and **Cliff's delta**.

For classification, six heavily missing F0-based features were excluded, leaving **70 retained features**. H3 uses the **32 retained rhythm + pitch features**.

Models were evaluated using **five-fold stratified grouped cross-validation**, ensuring that excerpts from the same AdP source track remained in the same fold. The classifiers were:

- majority-class baseline
- logistic regression
- radial basis function (RBF) support vector machine (SVM)

## Results

### H1 Rhythmic irregularity

| AdP median | GTZAN rock median | p-value | Cliff's delta |
|---:|---:|---:|---:|
| 0.418 | 0.481 | 0.160 | -0.25 |

H1 was **not supported**. The observed direction was opposite to the hypothesis.

### H2 Microtonality

| AdP median | GTZAN rock median | p-value | Cliff's delta |
|---:|---:|---:|---:|
| 11.750 cents | 10.359 cents | 0.504 | 0.13 |

H2 was **not supported statistically**. AdP showed slightly greater pitch deviation, but the difference was weak and non-significant.

### H3 Rhythm + pitch classification

| Model | Accuracy | F1 |
|---|---:|---:|
| Majority baseline | 0.515 | 0.000 |
| Logistic regression | 0.873 | 0.870 |
| RBF SVM | **0.904** | **0.903** |

The RBF SVM correctly classified **89 of 96 AdP excerpts** and **88 of 100 rock excerpts** under grouped cross-validation.

H3 was therefore **supported**.

## Feature-Family Comparison

| Features | Logistic Regression F1 | RBF SVM F1 |
|---|---:|---:|
| Rhythm | 0.711 | 0.715 |
| Pitch | 0.840 | 0.841 |
| Rhythm + Pitch | 0.870 | 0.903 |
| Timbre | **0.977** | 0.958 |
| All retained features | 0.952 | **0.967** |

The strongest single feature family was **timbre**, suggesting that broader spectral and timbral characteristics provide stronger separation in this dataset than rhythm or pitch alone.

A recording-bandwidth check showed a measurable high-frequency difference between AdP and GTZAN. However, after both datasets were low-pass filtered at 8 kHz, timbre classification remained strong (F1 = 0.960 for logistic regression and 0.974 for the SVM). The high-frequency difference therefore does not by itself explain the timbre result.

## Conclusion

AdP has a clearly distinguishable audio profile relative to the GTZAN rock comparison set.

However, the results do **not** support the conclusion that this distinction is primarily caused by greater rhythmic irregularity or microtonality. H1 and H2 were not statistically supported, while H3 showed that broader rhythm and pitch information still provides strong multivariate separation. Timbre was the most discriminative single feature family.

This remains a **case study**, not a general detector of unconventional rock. The positive class contains only one artist and 12 independent AdP tracks, so classification may also capture artist-specific instrumentation, production, vocal characteristics, and recording differences.

## Running the Notebook

The analysis is contained in the project notebook.

Main Python dependencies include:

```bash
pip install numpy pandas scipy matplotlib librosa soundfile scikit-learn tqdm jupyter
```

Then start Jupyter and run the notebook from top to bottom:

```bash
jupyter notebook
```

The raw Angine de Poitrine recordings are **not included** in the public repository. They were purchased digitally and processed locally. Users wishing to reproduce the analysis must provide their own legally obtained audio files.

## Authors

- Johanna Genander
- Fanny Nyberg
- Filip Svebeck

Project completed for **DT2470 Music Informatics** at KTH Royal Institute of Technology.
