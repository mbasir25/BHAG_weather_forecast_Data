# BHAG Weather Forecast Data

This repository contains a Python-based workflow for downloading, processing, and storing short-range weather forecast data in JSON format. The script retrieves forecast data from NOAA/NWS gridded forecast files, processes selected weather variables, spatially filters the data for a defined geographic region, calculates regional average forecast values, and appends the results to a structured JSON file.

The repository is designed as a lightweight weather-data collection pipeline that can support agricultural forecasting, Ag IoT applications, decision-support tools, and model pipelines that require regularly updated forecast information.

---

## Repository Overview

Agricultural decision systems often require weather information that is not generated directly within a local IoT environment. Forecast data can provide external model-derived information such as expected temperature, precipitation probability, and wind speed. This repository provides a simple workflow for collecting such forecast data and storing it in a reusable format.

The main script downloads NOAA forecast binary files, reads GRIB-formatted weather data, extracts variables over a specified latitude-longitude range, calculates average values, and saves the forecast outputs as a JSON file. The resulting JSON can be used by downstream analytics, dashboards, crop models, or IoT-connected decision systems.

---

## Main Objectives

The main objectives of this repository are:

1. To download short-range weather forecast files from NOAA/NWS forecast data sources.
2. To process GRIB weather forecast files using Python.
3. To extract forecast variables for a selected geographic region.
4. To calculate regional average forecast values.
5. To store daily forecast outputs in a structured JSON format.
6. To support agricultural forecasting and Ag IoT decision-support workflows.

---

## Project Workflow

```text
NOAA/NWS forecast source
        ↓
Download GRIB/binary forecast files
        ↓
Read forecast data using pygrib
        ↓
Extract variables and forecast dates
        ↓
Filter by latitude-longitude boundary
        ↓
Calculate regional average values
        ↓
Append daily forecast data to JSON
        ↓
Use forecast JSON in downstream models or tools
```

---

## Repository Structure

```text
BHAG_weather_forecast_Data/
│
├── .github/
│   └── workflows/
│       └── GitHub Actions workflow files, if configured
│
├── weatherdata/
│   └── news_refs/
│       └── Folder used to store downloaded forecast files and generated JSON output
│
├── forecast_script_json.py
│   └── Main Python script for downloading and processing forecast data
│
├── environment.yml
│   └── Conda environment specification
│
├── requirements.txt
│   └── Python package requirements
│
├── LICENSE
│   └── CC0-1.0 License
│
└── README.md
    └── Repository documentation
```

---

## Main Script

### `forecast_script_json.py`

The main script performs two major tasks:

1. **Download forecast files**
   - Downloads NOAA/NWS binary forecast files for two forecast windows:
     - Days 1–3
     - Days 4–7
   - Stores downloaded files in the local working directory.

2. **Generate JSON forecast data**
   - Opens downloaded GRIB files using `pygrib`.
   - Extracts forecast variables, units, forecast dates, latitude, longitude, and grid values.
   - Filters the forecast grid to a defined region.
   - Calculates average forecast values over that region.
   - Saves or updates a JSON file named `forecasted_weather.json`.

---

## Forecast Variables

The script is designed to process the following forecast variables:

| Source File | Variable Meaning |
|---|---|
| `ds.maxt.bin` | Maximum temperature |
| `ds.mint.bin` | Minimum temperature |
| `ds.pop12.bin` | 12-hour probability of precipitation |
| `ds.wspd.bin` | Wind speed |

The script downloads these variables for both the 1–3 day forecast window and the 4–7 day forecast window.

---

## Forecast Windows

The workflow separates forecast data into two forecast periods:

```text
Days 1–3 forecast:
    VP.001-003

Days 4–7 forecast:
    VP.004-007
```

These forecast periods allow the script to build a short-range forecast dataset covering approximately one week.

---

## Spatial Filtering

The script filters the gridded forecast data using a latitude-longitude boundary. In the current version, the spatial filter is:

```python
(lats >= 39.5) & (lats <= 40.4) & (lons >= -87.4) & (lons <= -86.5)
```

This region can be modified depending on the study area or deployment location.

To use a different region, update the latitude and longitude limits inside `forecast_script_json.py`.

---

## JSON Output Structure

The output is saved as:

```text
weatherdata/news_refs/forecasted_weather.json
```

The JSON structure is organized by the date on which the script is run, followed by forecast dates and forecast variables.

A simplified example structure is:

```json
{
    "2025-05-20": {
        "2025-05-21": {
            "Maximumtemperature(K)": 295.123,
            "Minimumtemperature(K)": 284.456,
            "PoP(%)": 35.0,
            "Windspeed(m/s)": 4.25
        },
        "2025-05-22": {
            "Maximumtemperature(K)": 296.230,
            "Minimumtemperature(K)": 285.120,
            "PoP(%)": 42.5,
            "Windspeed(m/s)": 5.10
        }
    }
}
```

The exact variable names and units depend on the GRIB metadata.

---

## Requirements

The repository uses Python and the following main packages:

```text
requests
pygrib
pandas
```

The conda environment also includes:

```text
python=3.8
pygrib
pandas
requests
libtiff
```

`libtiff` is included in the conda environment to help resolve shared-object dependency issues that may occur when using `pygrib`.

---

## Installation

### Option 1: Install with Conda

Create the conda environment from `environment.yml`:

```bash
conda env create -f environment.yml
conda activate weather-env
```

### Option 2: Install with pip

Install the required Python packages:

```bash
pip install -r requirements.txt
```

If `pygrib` installation fails with pip, using conda is recommended:

```bash
conda install -c conda-forge pygrib
```

---

## How to Use

### 1. Clone the Repository

```bash
git clone https://github.com/mbasir25/BHAG_weather_forecast_Data.git
cd BHAG_weather_forecast_Data
```

### 2. Set Up the Environment

Using conda:

```bash
conda env create -f environment.yml
conda activate weather-env
```

or using pip:

```bash
pip install -r requirements.txt
```

### 3. Run the Forecast Script

```bash
python forecast_script_json.py
```

The script will:

1. Create the working directory if it does not exist.
2. Download forecast files.
3. Process GRIB forecast data.
4. Generate or update `forecasted_weather.json`.

---

## Output Location

By default, the script saves data inside:

```text
weatherdata/news_refs/
```

The main JSON output is:

```text
forecasted_weather.json
```

Downloaded forecast files are also stored in the same working folder.

---

## Application Context

This repository can be useful for:

- Agricultural weather forecasting
- Ag IoT decision-support systems
- Crop monitoring workflows
- Drone operation scheduling
- Weather-driven irrigation decisions
- Pest and disease risk forecasting
- Crop-yield modeling
- Forecast abstraction services
- External model integration in IoT environments

---

## Conceptual Use in Ag IoT

In an Ag IoT-connected environment, local sensors may provide internal data such as soil moisture, air temperature, field observations, and equipment status. However, some decisions require external forecast information that the IoT environment does not produce by itself.

This repository can function as a simple forecast-data provider for such systems.

```text
NOAA/NWS Forecast Model
        ↓
BHAG Weather Forecast Data Script
        ↓
Forecasted Weather JSON
        ↓
Ag IoT System / Dashboard / Model
        ↓
Decision Support
```

Examples of downstream decisions include:

- Should a drone fly today?
- Is wind speed suitable for spraying?
- Is rainfall likely within the next few days?
- Is irrigation needed?
- Are weather conditions favorable for disease risk?
- How might upcoming weather affect crop growth?

---

## Customization

Users can modify the script for different purposes.

### Change the Region

Update the latitude and longitude filter:

```python
filter_condition = (
    (lats >= min_lat) &
    (lats <= max_lat) &
    (lons >= min_lon) &
    (lons <= max_lon)
)
```

### Add More Weather Variables

Additional NOAA/NWS GRIB files can be added to the download dictionaries in the script.

### Change Output Format

The current output is JSON, but the workflow can be modified to export:

- CSV
- GeoJSON
- Parquet
- Database tables
- API-ready JSON

### Automate Daily Runs

The script can be scheduled using:

- GitHub Actions
- cron jobs
- Windows Task Scheduler
- Cloud functions
- Server-based automation

---

## Notes for Users

- The script depends on the availability of NOAA/NWS forecast files at the source URLs.
- Forecast files may change naming conventions, availability, or server structure over time.
- Internet access is required to download forecast data.
- `pygrib` can be sensitive to system libraries; conda installation is usually more reliable.
- The current spatial boundary is hard-coded and should be changed for new locations.
- The JSON file is updated by adding the latest run date as a new top-level key.

---

## Potential Future Improvements

Future improvements may include:

- Adding more forecast variables
- Supporting user-defined regions
- Exporting forecast data as CSV and GeoJSON
- Adding spatial aggregation by county, field, or farm boundary
- Adding automatic daily execution through GitHub Actions
- Building an API endpoint for forecast data access
- Integrating with Ag IoT dashboards
- Adding drone flight suitability logic
- Adding crop-stress or disease-risk indicators
- Improving error handling and logging
- Adding unit conversion for temperature and wind speed
- Adding forecast visualization tools

---

## License

This repository is licensed under the CC0-1.0 license. See the `LICENSE` file for details.

---

## Author

**Md. Samiul Basir**  
Ph.D. Candidate  
Department of Agricultural and Biological Engineering  
Purdue University  

GitHub: `mbasir25`

---

## Suggested Citation

If you use or adapt this repository, please cite it as:

```bibtex
@misc{basir_bhag_weather_forecast_data,
  author = {Basir, Md. Samiul},
  title = {BHAG Weather Forecast Data},
  year = {2025},
  publisher = {GitHub},
  url = {https://github.com/mbasir25/BHAG_weather_forecast_Data}
}
```

---

## Disclaimer

This repository is intended for research, prototyping, and educational purposes. Forecast outputs should be validated before use in operational agricultural decision-making, drone scheduling, irrigation management, or other high-stakes applications.
