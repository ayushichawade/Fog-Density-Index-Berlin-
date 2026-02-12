# 🌫️ Fog Detection Index (FDI) Analysis for Berlin

## 📌 Project Overview

This project develops a **Fog Detection Index (FDI)** to estimate fog presence and severity in Berlin using meteorological data and geospatial analysis.

The FDI combines:

- 🌡️ Temperature  
- 💧 Dew Point  
- 💨 Wind Speed (10m)  
- 🌫️ Relative Humidity  

The goal is to:
- Analyze fog formation potential
- Normalize weather parameters for consistent scaling
- Map fog intensity across urban road networks
- Support driving-related fog risk assessment

---

## 🗺️ Study Area

The analysis focuses on **Berlin (Mitte district)** using:

- OpenStreetMap (OSM) road network data
- A generated spatial grid over the district
- Hourly meteorological data from Open-Meteo API

---

## 📊 Methodology

### 1️⃣ Road Network Extraction

- Berlin `.pbf` file downloaded from **Geofabrik**
- Processed using `pyrosm`
- Roads filtered by highway types:
  - motorway, trunk, primary
  - secondary, tertiary
  - residential, unclassified
  - link roads

This allows integration of fog risk with urban infrastructure.

---

### 2️⃣ Meteorological Data Collection

Weather data is fetched using the **Open-Meteo API**.

**Variables used:**

- `temperature_2m`
- `dew_point_2m`
- `relative_humidity_2m`
- `wind_speed_10m`

Data is collected hourly for each grid cell centroid.

---

## 🧮 Fog Detection Index (FDI)

### Step 1: Preprocessing

- Convert wind speed from km/h → m/s  
- Compute **Dew Point Depression**:

ΔTd = T − Td

Where:
- `T` = temperature
- `Td` = dew point temperature

---

### Step 2: Normalization (0–1 scale)

```python
weather_df['RH_norm'] = ((weather_df['relative_humidity_2m'] - 95)/5).clip(0,1)
weather_df['D_norm'] = (1 - (weather_df['delta_Td'] / 2)).clip(0,1)
weather_df['W_norm'] = (1 - (weather_df['U'] / 5)).clip(0,1)
```

- High RH → higher fog probability  
- Small dew point depression → higher fog probability  
- Low wind speed → higher fog persistence  

---

### Step 3: FDI Formula

FDI = 0.4 · RH_norm + 0.4 · D_norm + 0.2 · W_norm

The weights reflect:
- 40% Relative Humidity
- 40% Dew Point Depression
- 20% Wind Speed

---

## 🚗 Visibility Classification

FDI values are mapped to estimated visibility and driving conditions:

| FDI Range | Visibility | Condition |
|------------|------------|------------|
| < 0.3 | > 1000 m | Clear |
| 0.3 – 0.5 | 500–1000 m | Light fog |
| 0.5 – 0.7 | 200–500 m | Moderate fog |
| 0.7 – 0.9 | 50–200 m | Dense fog |
| > 0.9 | < 50 m | Very dangerous |

---

## 📅 Case Studies

### 🗓️ 06 November 2024
- Official fog warnings issued in Berlin
- Used as a real-world validation scenario
- FDI successfully captured high fog intensity

### 🗓️ 06 February 2026
- Lower fog intensity
- Used for comparison
- Demonstrated consistent FDI behavior under clearer conditions

---

## 🗺️ Visualization

The project includes:

- Interactive **Folium maps**
- Road network overlays
- Grid-based fog intensity visualization
- Color-coded road classifications
- Time-based heatmaps

---

## 🛠️ Technologies Used

- Python
- Pandas
- GeoPandas
- Pyrosm
- Folium
- Branca
- Open-Meteo API
- Requests-cache
- NumPy

---

## 📂 Project Structure

```
│── urbantech_fog.ipynb
│── berlin-260205.osm.pbf
│── weather_data.csv
│── README.md
```

---

## 🔬 Scientific Basis

The methodology is based on:

- Dew Point Depression theory
- Relative humidity saturation thresholds
- Wind-speed influence on fog persistence
- Multi-parameter fog detection approaches in atmospheric research

References include:
- Imteaz et al. (2011)
- REAL-Fog research framework
- Operational fog diagnostics literature

---

## ✅ Conclusion

This project presents a structured, reproducible framework for:

- Estimating fog presence
- Classifying fog severity
- Mapping fog risk in urban road networks
- Supporting transportation safety analysis

The Fog Detection Index (FDI) demonstrates consistent behavior under both high-fog and low-fog conditions, making it suitable for urban climate and mobility risk applications.

---

## 🚀 Future Improvements

- Integrate real visibility sensor data
- Apply machine learning calibration
- Extend to other cities
- Real-time fog monitoring dashboard
- Traffic accident correlation analysis

---

## 📬 Author

Developed as part of an Urban Technology and Geospatial Analysis study project.
