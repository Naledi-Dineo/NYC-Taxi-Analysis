# NYC Taxi Demand & Spatial-Temporal Mobility Analysis

Large-scale exploratory data analysis, geospatial clustering, and dimensionality reduction on **1.45M+ New York City taxi trip records** (Jan–Jun 2016, sourced from the Kaggle NYC Taxi / Uber mobility dataset) to uncover temporal demand patterns, urban congestion trends, and high-density pickup hotspots.

## Project Overview
Urban mobility datasets contain millions of GPS-logged journeys alongside sensor anomalies, extreme durations, and spatial outliers. This project implements an end-to-end analytical pipeline in Python to validate and clean raw trip logs, engineer geospatial and cyclical temporal features, identify street-level pickup clusters via Haversine DBSCAN, and benchmark linear vs. non-linear dimensionality reduction techniques (PCA, t-SNE, UMAP) paired with HDBSCAN clustering.

## Data Cleaning & Feature Engineering
* **Anomaly & Outlier Filtering:** Inspected **1,458,644 raw trips** across 11 variables. Isolated and removed **9,570 (0.656%)** implausible records—including 24-hour taximeter timeouts ($0.068\text{ km/h}$ median speed), speeds exceeding $100\text{ km/h}$, trips under 10 seconds or over 4 hours, zero-displacement logs, and invalid passenger counts—retaining **1,449,074 (99.344%)** validated journeys.
* **Geospatial & Speed Metrics:** Computed great-circle trip distances using the **Haversine formula** and derived average journey speeds ($\text{km/h}$) from trip durations.
* **Cyclical Temporal Encoding:** Extracted pickup day of week, weekday/weekend flags, and pickup hour, applying sine/cosine transformations to preserve periodic proximity across midnight boundaries (e.g., 23:00 to 00:00).

## Key Analytical Insights
* **Temporal Demand & Nightlife Shifts:** Taxi demand builds steadily across the workweek to peak on **Friday** (highest single-hour volume at **Friday 19:00** with 14,075 trips). Weekday demand peaks during evening commutes (**18:00–21:00**), whereas weekend peaks shift to late-night leisure hours (**Saturday 23:00** and **Sunday 00:00**).
* **Urban Congestion Dynamics:** Average straight-line speeds peak at **$21.15\text{ km/h}$ at 05:00** during free-flow traffic before dropping by more than half to a daytime low of **$9.73\text{ km/h}$ at 15:00**.
* **Airport Travel Time Variability:** Analysing journeys from the Empire State Building ($1\text{ km}$ pickup radius) to JFK and Newark ($2\text{ km}$ drop-off radius) revealed shortest travel times at **03:00** ($27.89\text{ min}$ to JFK; $26.21\text{ min}$ to Newark) and severe afternoon congestion peaks at **16:00–17:00** ($65.46\text{ min}$ to JFK; $51.88\text{ min}$ to Newark).
* **Spatial Hotspot Clustering (DBSCAN):** Using Haversine distance ($\epsilon = 50\text{ m}$, $\text{min\_samples} = 30$) after sensitivity testing across $50\text{ m}–200\text{ m}$ radii, identified **181 clusters** during Thursday evening commutes (spanning Midtown and Upper Manhattan) vs. **142 clusters** during Friday late-night hours (concentrated in Midtown and Lower Manhattan), alongside persistent hotspots at JFK and LaGuardia airports.

## Dimensionality Reduction & HDBSCAN Benchmarking
Evaluated **PCA**, **t-SNE**, and **UMAP** on 20,000 standardised trips across 10 spatial, temporal, and journey features, followed by **HDBSCAN** clustering ($\text{min\_cluster\_size} = 100$, $\text{min\_samples} = 10$):

| Method | Configuration | HDBSCAN Clusters | Noise (%) | Runtime (s) | Key Takeaway |
| :--- | :--- | :---: | :---: | :---: | :--- |
| **PCA** | 8 components (**94.54%** variance) | 7 | 35.04% | **0.12s** | Fastest; interpretable linear axes (journey scale, latitude, pickup hour) but high noise rate. |
| **t-SNE** | `perplexity=100` | 5 | 0.005% | 561.43s | Strong visual separation of JFK trips, but computationally expensive. |
| **UMAP** | `n_neighbors=15`, `min_dist=0.0` | **8** | 0.38% | 25.77s | **Best overall balance:** ~21.8x faster than t-SNE with strong partition agreement ($\text{ARI} = 0.892$). |

* **Cross-Method Cluster Profiles:** All three methods consistently separated short urban trips ($1–2\text{ km}$), high-occupancy group rides (median 5 passengers), medium-distance journeys ($\sim 12\text{ km}$), and long-distance/JFK airport transfers ($\sim 21–22\text{ km}$, with 84%–92% JFK trip purity in dedicated long-haul clusters).

## Tech Stack
* **Language:** Python
* **Libraries:** Pandas, NumPy, Scikit-learn (PCA, t-SNE, DBSCAN, Adjusted Rand Index), UMAP (`umap-learn`), HDBSCAN (`hdbscan`), Matplotlib, Seaborn

## Repository Structure
* `DVE.ipynb` — Full Jupyter Notebook containing data cleaning, Haversine feature engineering, temporal/geospatial visualisations, DBSCAN sensitivity analysis, and PCA/t-SNE/UMAP + HDBSCAN experiments.
* `Assignment 2.pdf` — Technical report (co-authored with Ntokozo Skosana) detailing methodology, visual comparisons, and clustering evaluation.
