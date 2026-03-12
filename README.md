# Quantifying Forest Loss in the Jamanxim National Forest with Computer Vision Methods

**Author:** Toni Esteve Gene  
**Supervisor:** Dr. Alexandre Pérez Reina  
**Department:** Department of Computer Science  
**Institution:** Universidad Internacional de La Rioja (UNIR)  
**Year:** 2025  

## Overview

This project presents an automated computer vision workflow for **quantifying deforestation in the Jamanxim National Forest (Brazil)** over the period **2000–2019**. Using **Python** and **OpenCV**, the method processes a time series of satellite images to detect, segment, and measure deforested regions.

The pipeline combines:

- cloud masking in **HSV** and **Lab** color spaces,
- selective contrast enhancement,
- **seed-based region-growing segmentation**,
- morphological postprocessing,
- and conversion from pixel counts to physical area measurements.

The final goal is to estimate the extent of forest loss in square kilometers and analyze its temporal evolution.

---

## Objectives

- Build an automated and reproducible workflow for deforestation monitoring.
- Detect deforested regions from satellite imagery using image segmentation techniques.
- Quantify forest loss in **km²** from a calibrated pixel-to-distance scale.
- Evaluate the strengths and limitations of the method under different atmospheric and visual conditions.

---

## Dataset

The study uses a temporal series of **20 satellite images** of the **Jamanxim National Forest**, covering the years **2000 to 2019**.

These images were extracted from an animated GIF and stored as individual yearly frames with homogeneous dimensions and format.

### Spatial Resolution

The scale used in the study is:

- **20 km ≈ 51 pixels**
- therefore, **1 pixel ≈ 0.392 km**
- and **1 pixel² ≈ 0.153 km²**

This calibration allows the conversion of segmented image regions into real deforested area estimates.

---

## Methodology

## 1. Cloud Masking

Clouds are detected using thresholding rules in the **HSV** and **Lab** color spaces.

Cloud pixels are identified by:

- high brightness/value,
- high lightness,
- low saturation.

A binary cloud mask is created and refined using **morphological closing** to fill internal gaps and reduce fragmented cloud regions.

---

## 2. Selective Contrast Enhancement

To improve the distinction between vegetation and deforested land, the method selectively enhances non-green regions in the **HSV** space.

A vegetation mask is first defined using thresholds over:

- **Hue**
- **Saturation**
- **Value**

Then, for non-vegetation pixels, saturation and brightness are amplified using fixed factors:

- `β_s = 1.55`
- `β_v = 1.25`

This step increases the visual contrast between forest mass and exposed or degraded terrain.

---

## 3. Seed-Based Region Growing Segmentation

After enhancement, the main segmentation stage is applied.

The algorithm follows a **bottom-up region-growing strategy**:

- seeds are selected automatically from pixels that are most dissimilar to vegetation in HSV space,
- a minimum spatial distance between seeds is enforced,
- regions expand recursively according to a similarity threshold.

### Main Parameters

- maximum number of seeds: **3000**
- seed spacing constraint: **6 pixels**
- growth threshold: **τ = 12**

This produces a binary segmentation mask representing the deforested regions.

---

## 4. Morphological Postprocessing

Because region-growing results may contain fragmented or discontinuous areas, the binary segmentation is refined using **morphological closing**:

$$
A \bullet B = (A \oplus B) \ominus B
$$

where:

- $$A$$ is the segmented image,
- $$B$$ is an elliptical structuring element.

In this project, a **3×3 closing window** is used to connect nearby regions and reduce small holes.

---

## 5. Area Quantification

The final binary mask is converted into physical area measurements.

If $$N_{px}$$ is the number of active pixels classified as deforested, then the total area is computed as:

$$
\text{Deforested Area} = N_{px} \times S^2
$$

with:

$$
S \approx 0.392 \text{ km/pixel}
$$

This gives the total deforested area in **square kilometers**.

---

## Main Results

The results show a clear long-term increase in forest loss.

- **2000:** 1876.66 km²
- **2019:** 7450.91 km²

This means that the deforested area **more than tripled** over two decades.

### General Trend

The most notable acceleration periods were:

- **2004–2008**
- **from 2016 onward**

The workflow successfully captures the broad temporal progression of deforestation in the region.

---

## Limitations

Although the method performs well under good visibility conditions, the study identifies several important limitations:

### 1. Dense Cloud Cover
Cloud masking may exclude areas that are actually deforested but hidden below opaque clouds.

### 2. Translucent Haze
Thin whitish haze may not be fully detected by standard cloud thresholds, reducing segmentation quality.

### 3. Secondary Vegetation Regrowth
Greenish tones in previously disturbed areas can reduce the contrast between forest and deforested land, leading to underestimation.

### 4. Lower-Bound Estimation
Because of the above issues, the detected area should often be interpreted as a **reliable lower bound** rather than an exact measurement.

---

## Requirements

The project is based on **Python** and **OpenCV**.

### Suggested dependencies

```bash
pip install opencv-python numpy matplotlib
````

Depending on the implementation, you may also need:

```bash
pip install pillow
```

---

## How to Run

Since the article describes the workflow conceptually, a typical execution pipeline would look like this:

1. Extract the yearly frames from the GIF.
2. Load each frame in Python.
3. Apply cloud masking.
4. Perform selective contrast enhancement.
5. Generate seeds and run region-growing segmentation.
6. Apply morphological closing.
7. Count segmented pixels.
8. Convert the result to **km²**.
9. Repeat for all years and plot the temporal evolution.

A simplified expected folder structure could be:

```text
project/
├── data/
│   ├── frame_000.png
│   ├── frame_001.png
│   ├── ...
│   └── frame_019.png
├── scripts/
│   ├── extract_frames.py
│   ├── cloud_mask.py
│   ├── enhance.py
│   ├── segment.py
│   └── quantify.py
├── results/
│   ├── masks/
│   ├── overlays/
│   └── plots/
└── README.md
```

---

## Output

The workflow is expected to generate:

* cloud masks,
* enhanced images,
* binary segmentation masks,
* overlay visualizations,
* yearly deforestation estimates in **km²**,
* and a final plot of forest loss over time.

---

## Conclusions

This project shows that classical computer vision techniques can provide a practical and interpretable approach for monitoring deforestation from satellite imagery.

The proposed workflow is:

* automated,
* reproducible,
* computationally lightweight,
* and effective under standard atmospheric conditions.

However, its performance decreases in the presence of:

* dense clouds,
* translucent haze,
* and vegetation regrowth.

Future improvements could include:

* dehazing algorithms,
* more robust vegetation indices,
* adaptive thresholding,
* and more advanced segmentation methods.

---

## Future Work

Possible extensions of the project include:

* integrating atmospheric correction or dehazing,
* improving cloud detection,
* incorporating spectral or vegetation indices,
* testing machine learning or deep learning segmentation models,
* and validating results against external geospatial reference data.

---

## Acknowledgments

This README is based on the academic work:

**Quantifying Forest Loss in the Jamanxim National Forest with Computer Vision Methods**
Toni Esteve Gene, 2025
Universidad Internacional de La Rioja (UNIR)

---

