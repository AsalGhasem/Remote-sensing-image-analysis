# Remote-sensing-image-analysis

A Python-based remote sensing analysis project covering **Landsat multispectral image preprocessing, spectral indices, land surface temperature estimation, cloud detection, geometric correction, and image classification**.

The project implements the processing workflows directly with NumPy and Matplotlib, including radiometric conversion, spectral index calculation, emissivity estimation, thermal analysis, feature extraction, and both supervised and unsupervised classification methods.

## Overview

The workflow uses multispectral Landsat bands to perform several remote sensing analyses:

1. Load and visualize Landsat bands
2. Convert digital numbers (DN) to spectral radiance
3. Calculate vegetation, water, soil, moisture, and built-up indices
4. Perform Optimal Index Factor (OIF) analysis
5. Estimate land surface emissivity
6. Calculate brightness temperature
7. Estimate land surface temperature (LST)
8. Detect clouds using thermal and reflectance thresholds
9. Perform geometric correction using ground control points
10. Classify the image using multiple classification algorithms
11. Evaluate classification accuracy using Overall Accuracy and Kappa

## Processing Workflow

### 1. Radiometric Conversion

The Landsat bands are loaded as digital number (DN) images and converted to spectral radiance using the corresponding minimum and maximum radiance values.

The conversion is implemented using:

```text
Lλ = ((Lmax - Lmin) / DNmax) × DN + Lmin
```

Radiance images are generated for Landsat bands 1–7, 9, 10, and 11.

### 2. Spectral Indices

Several spectral indices are calculated from the multispectral bands.

#### Vegetation Indices

* **NDVI** — Normalized Difference Vegetation Index
* **SRVI** — Simple Ratio Vegetation Index
* **SAVI** — Soil Adjusted Vegetation Index
* **MSAVI**
* **MSAVI2**
* **EVI2**
* **EVI3**

#### Water Indices

* **NDWI** — Normalized Difference Water Index
* **MNDWI** — Modified Normalized Difference Water Index
* **AWEI** — Automated Water Extraction Index

#### Moisture and Soil Indices

* **NDMI** — Normalized Difference Moisture Index
* **NMDI** — Normalized Multi-band Drought Index
* **NDTI** — Normalized Difference Tillage Index
* **BSI** — Bare Soil Index

#### Built-up Indices

* **NDBI** — Normalized Difference Built-up Index
* **MNDBI** — Modified Normalized Difference Built-up Index

Each index is calculated pixel-by-pixel and visualized as a raster map.

## 3. Optimal Index Factor (OIF)

The project calculates the **Optimal Index Factor (OIF)** for combinations of three spectral bands.

The analysis considers the standard deviation of each band and the correlations between bands to identify combinations that provide high information content with relatively low redundancy.

The resulting band combinations are evaluated and ranked according to their OIF values.

## 4. Land Surface Temperature

The thermal bands are used to estimate **Land Surface Temperature (LST)**.

The workflow includes:

### Surface Emissivity

Three emissivity approaches are implemented:

* NDVI-based emissivity
* Logarithmic NDVI-based emissivity
* Fractional Vegetation Cover (FVC)-based emissivity

### Brightness Temperature

Brightness temperature is calculated from thermal radiance using the thermal constants of Landsat bands 10 and 11.

### LST Models

Several LST calculations are implemented using:

* Band 10 brightness temperature with NDVI-based emissivity
* Band 10 brightness temperature with logarithmic NDVI-based emissivity
* Band 10 brightness temperature with FVC-based emissivity
* A split-window model using bands 10 and 11

The resulting LST maps are visualized for comparison.

## 5. Cloud Detection

Cloud masks are generated using combinations of:

* Thermal brightness temperature
* Reflectance thresholds

Three threshold configurations are tested:

* Least strict
* Moderate
* Strict

This allows the effect of different temperature and reflectance thresholds on cloud detection to be examined.

## 6. Geometric Correction

The notebook also includes a geometric correction workflow using **Ground Control Points (GCPs)**.

The process includes:

* Loading GCP coordinates
* Displaying GCP locations on a SPOT image
* Filtering invalid or unsuitable points
* Fitting a polynomial transformation using least squares
* Calculating residuals
* Iteratively removing points with large residuals
* Evaluating the resulting RMSE

The transformation uses polynomial terms based on the ground coordinates:

```text
1, x, y, xy, x², y²
```

## 7. Image Classification

The multispectral bands are stacked into a single feature array, with pixels represented by their spectral values.

Six land-cover classes are used:

| Class | Description     |
| ----- | --------------- |
| 1     | Water           |
| 2     | Jungle / Forest |
| 3     | Agriculture     |
| 4     | Cloud           |
| 5     | Soil            |
| 6     | Residential     |

Regions of Interest (ROIs) are selected for each class and used to calculate class statistics.

### Classification Methods

The notebook implements and compares several classification approaches:

* **Parallelepiped Classification (PEC)**
* **Ellipsoid Classification (EC)**
* **Minimum Distance Classification (MDC)**
* **Maximum Likelihood Classification (MLC)**
* **Mahalanobis Distance Classification (MNC)**
* **K-Means Clustering**

The classification methods are implemented using NumPy rather than relying on a dedicated remote sensing classification library.

## 8. Accuracy Assessment

Classification results are evaluated using validation regions and a confusion matrix.

The following metrics are calculated:

* **Overall Accuracy (OA)**
* **Kappa Coefficient**

The obtained results are:

| Classification       | Overall Accuracy | Kappa |
| -------------------- | ---------------: | ----: |
| Parallelepiped       |           65.39% |  0.57 |
| Ellipsoid            |           81.28% |  0.74 |
| Minimum Distance     |           65.38% |  0.57 |
| Maximum Likelihood   |           81.66% |  0.77 |
| Mahalanobis Distance |           86.21% |  0.83 |

## Tech Stack

* **Python**
* **NumPy** — numerical computation, array operations, masking, and vectorization
* **Pandas** — GCP data handling
* **Matplotlib** — raster visualization and classification maps
* **Jupyter Notebook** — interactive analysis and visualization

## Project Structure

```text
remote-sensing-image-analysis/
│
├── main.ipynb
├── Residuals/
│   ├── B1.TIF
│   ├── B2.TIF
│   ├── ...
│   ├── B11.TIF
│   ├── SPOT image.jpg
│   └── GCPs of SPOT image.txt
│
└── README.md
```

## How to Run

### 1. Clone the repository

```bash
git clone https://github.com/your-username/remote-sensing-image-analysis.git
cd remote-sensing-image-analysis
```

### 2. Install the dependencies

```bash
pip install numpy pandas matplotlib jupyter
```

### 3. Prepare the input data

Place the required Landsat bands and GCP files inside the `Residuals/` directory.

The notebook expects files such as:

```text
Residuals/B1.TIF
Residuals/B2.TIF
Residuals/B3.TIF
...
Residuals/B11.TIF
Residuals/SPOT image.jpg
Residuals/GCPs of SPOT image.txt
```

Band 8 is not used in the current workflow.

### 4. Run the notebook

```bash
jupyter notebook main.ipynb
```

Execute the cells sequentially to reproduce the processing workflow and generated maps.
