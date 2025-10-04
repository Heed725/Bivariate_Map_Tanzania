
# 🌍 Bivariate Climate Map of Tanzania Using CHELSA Data in QGIS

This project demonstrates how to create a **2D bivariate map of Tanzania** showing the spatial relationship between **mean annual temperature** and **average monthly precipitation** using **CHELSA Bioclimatic data**. The entire workflow is done using **QGIS only — no coding or Python console** is required.

---

## 🧰 Requirements

- QGIS 3.28 or later
- Enabled QGIS plugins:
  - ✅ Processing Toolbox
  - ✅ SAGA (for raster reclassification)
  - ✅ QuickOSM (optional, to obtain Tanzania boundary)

---

## 📦 Input Data

| Dataset                 | Description                            |
|-------------------------|----------------------------------------|
| `CHELSA_bio10_01.tif`   | Mean annual temperature (°C × 10)       |
| `CHELSA_bio10_12.tif`   | Total annual precipitation (mm)        |
| `Tanzania_boundary.shp` | Tanzania national boundary (level 0)   |

🔗 Download CHELSA data from: [https://chelsa-climate.org/downloads/](https://chelsa-climate.org/downloads/)

---

## 🔧 Workflow Steps

### 1. Clip CHELSA Rasters to Tanzania Boundary

> `Raster > Extraction > Clip Raster by Mask Layer`

Repeat for both:
- `CHELSA_bio10_01.tif` → save as `temp_clipped.tif`
- `CHELSA_bio10_12.tif` → save as `prec_clipped.tif`

---

### 2. Convert Precipitation to Monthly Average

> `Raster > Raster Calculator`

Expression:
```text
"prec_clipped@1" / 30
````

Save as: `prec_avg.tif`

---

### 3. Reproject and Align Rasters (Optional but Recommended)

> `Processing > Toolbox > Warp (Reproject)`

* Target CRS: `EPSG:21037` (UTM Zone 37S - Tanzania)
* Resampling: Bilinear
* Align cell size and extent between rasters

---

### 4. Reclassify Both Rasters into 3 Quantile Classes

#### Step 1: Get Statistics

> `Raster > Miscellaneous > Raster Layer Statistics`

Record min, max, and compute class breaks manually.

#### Step 2: Reclassify by Table (SAGA)

> `Processing > Toolbox > Reclassify by Table`

* Use defined quantile breaks
* Assign:

  * Class 1 = Low
  * Class 2 = Medium
  * Class 3 = High

Save as:

* `temp_class.tif`
* `prec_class.tif`

---

### 5. Combine Reclassified Rasters into Bivariate Code

> `Raster > Raster Calculator`

Expression:

```text
"temp_class@1" * 10 + "prec_class@1"
```

Save as: `bivariate.tif`

This produces values from `11` to `33` representing each temperature-precipitation class combo.

---

### 6. Apply Bivariate Symbology

> `Layer Properties > Symbology`

1. Change **Render type**: `Singleband pseudocolor`
2. Switch to **Categorized**
3. Click **Classify** to list values `11` to `33`
4. Manually assign colors using the palette below

---

## 🎨 Bivariate Color Codes (DkBlue Palette)

| Bivariate Value | Class Combo                 | Color Hex |
| --------------- | --------------------------- | --------- |
| **11**          | Low Temp, Low Precipitation | `#e8e8e8` |
| **12**          | Low Temp, Medium Precip     | `#dfb0d6` |
| **13**          | Low Temp, High Precip       | `#be64ac` |
| **21**          | Medium Temp, Low Precip     | `#ace4e4` |
| **22**          | Medium Temp, Medium Precip  | `#a5add3` |
| **23**          | Medium Temp, High Precip    | `#8c62aa` |
| **31**          | High Temp, Low Precip       | `#5ac8c8` |
| **32**          | High Temp, Medium Precip    | `#5698b9` |
| **33**          | High Temp, High Precip      | `#3b4994` |

Assign these manually in the **categorized symbology** panel in QGIS.

---

### 7. Add Map Layout and Export

> `Project > New Print Layout`

* Add Map Frame
* Optional: Add custom bivariate legend (3×3 grid)
* Add:

  * Title: `Tanzania: Temperature and Precipitation`
  * Subtitle: `CHELSA Bioclim (1981–2010), Monthly Avg. Precipitation`
  * Caption: `Source: CHELSA | Author: Your Name`
* Export as `.png`, `.svg`, or `.pdf`

---

## 🧾 Suggested Folder Structure

```text
tanzania_bivariate_map/
├── CHELSA_bio10_01.tif
├── CHELSA_bio10_12.tif
├── Tanzania_boundary.shp
├── temp_clipped.tif
├── prec_clipped.tif
├── prec_avg.tif
├── temp_class.tif
├── prec_class.tif
├── bivariate.tif
├── qgis_project.qgz
└── README.md
```

---

## 📚 References

* **CHELSA Data**: [https://chelsa-climate.org](https://chelsa-climate.org)
* **QGIS**: [https://qgis.org](https://qgis.org)
* **Bivariate Colors**: Inspired by [`biscale::bi_pal("DkBlue")`](https://cran.r-project.org/web/packages/biscale/vignettes/biscale.html)
* **Mapping Credits**: Adapted by Milos Popovic (original R workflow)

---

## 🏁 Output

A clean 2D bivariate raster map showing the spatial co-distribution of temperature and precipitation across Tanzania.

---


