# Streamlit Economic Explorer

A browser-based Python application for exploring World Bank GDP indicators across countries, regions and time.

[Open the live application](https://puwomdwa9smu6qspnbxtot.streamlit.app/) · [View the source code](https://github.com/neeilnandal/streamlit-economic-explorer)

## Project snapshot

| Area | Implementation |
|---|---|
| Problem | World Bank indicators require retrieval, cleaning and metadata enrichment before they are convenient to compare |
| Solution | An interactive Streamlit application with country, region, metric and year filters |
| Data | World Bank API or a selectable local CSV dataset |
| Outputs | Time-series charts, KPI cards, regional comparisons, rankings, growth calculations and filtered CSV export |
| Stack | Python, Streamlit, Pandas and Requests |
| Scope | Exploratory analysis; no forecasting or causal inference |

## What the application does

The application retrieves and prepares economic indicators, then lets users:

- Compare countries using readable names and ISO country codes.
- Filter observations by World Bank region.
- Select a year range between 1960 and 2024.
- Explore nominal GDP, GDP per capita or constant-price GDP.
- Review country-level values and percentage growth.
- Compare regional totals over time.
- generate top-N country rankings for a selected year.
- Inspect and download the active filtered dataset.

The downloaded CSV is created from the same filtered dataframe used by the main country-level view.

## Indicators

| Metric | World Bank code | Unit |
|---|---|---|
| GDP | `NY.GDP.MKTP.CD` | Current US dollars |
| GDP per capita | `NY.GDP.PCAP.CD` | Current US dollars per person |
| Constant-price GDP | `NY.GDP.MKTP.KD` | Constant 2015 US dollars |

Constant-price GDP is useful for comparisons across time because it reduces the effect of price-level changes. It should not be interpreted as a causal measure of economic performance.

## Data workflow

1. The user selects World Bank API mode or Local CSV mode.
2. API mode retrieves paginated indicator records and country metadata.
3. Local mode reads `data/gdp_data.csv` and reshapes the year columns into long format.
4. Country records are enriched with names, regions and income classifications when metadata is available.
5. The application applies the selected region, country, metric and year filters.
6. The filtered data feeds the charts, KPI cards, rankings and CSV download.

API responses and prepared datasets are cached with `st.cache_data` to avoid repeating unchanged retrieval and transformation work.

## Reliability and data handling

The API client:

- Uses a 20-second request timeout.
- Calls `raise_for_status()` for unsuccessful HTTP responses.
- Shows a warning when a request fails.
- Returns an empty dataframe when the requested data cannot be prepared.
- Stops the application with a clear message when no usable data is available.
- Converts year and indicator fields to numeric types.
- Avoids division by zero in growth calculations.

The application uses public aggregate data. It does not collect credentials, accept file uploads or require an API key.

## Local CSV mode

Local mode expects:

```text
data/gdp_data.csv
```

The file must contain a `Country Code` column and year columns such as:

```text
Country Code,1960,1961,1962,...,2022
DEU,...
FRA,...
```

The application reshapes these year columns into:

```text
Country Code | Year | GDP current US$
```

Local mode supports nominal GDP. GDP per capita and constant-price GDP remain unavailable unless compatible fields and loading logic are added.

## Repository structure

```text
streamlit-economic-explorer/
├── data/
│   └── gdp_data.csv
├── .gitignore
├── LICENSE
├── README.md
├── requirements.txt
└── streamlit_app.py
```

`streamlit_app.py` is the application entry point and is intentionally kept in the repository root for Streamlit Community Cloud deployment.

## Run locally

### 1. Clone the repository

```bash
git clone https://github.com/neeilnandal/streamlit-economic-explorer.git
cd streamlit-economic-explorer
```

### 2. Create a virtual environment

```bash
python -m venv .venv
```

Activate it on macOS or Linux:

```bash
source .venv/bin/activate
```

Activate it in Windows PowerShell:

```powershell
.\.venv\Scripts\Activate.ps1
```

Activate it in Windows Command Prompt:

```bat
.venv\Scripts\activate.bat
```

### 3. Install the dependencies

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

### 4. Start the application

```bash
streamlit run streamlit_app.py
```

Streamlit will display the local address in the terminal, normally:

```text
http://localhost:8501
```

## Analytical limitations

- World Bank observations may be missing or revised.
- Percentage growth requires valid observations for both selected boundary years and a non-zero starting value.
- Regional charts sum the available country observations in each World Bank region; they are not official precomputed regional aggregates.
- Current-dollar GDP is affected by inflation and exchange-rate movements.
- GDP per capita does not measure income distribution or wellbeing.
- The application supports exploration, not forecasting, policy evaluation or causal inference.
- The repository does not currently include automated tests or continuous integration.

## Next engineering steps

The most useful improvements would be:

- Add unit tests for reshaping, filtering and growth calculations.
- Add contract tests for World Bank API response handling.
- Validate the local CSV schema with explicit error messages.
- Separate retrieval, transformation and presentation logic into smaller modules.
- Pin dependency versions for repeatable deployments.
- Add retry and backoff behaviour for temporary API failures.

These are planned improvements, not current capabilities.

## Data source

Indicator values and country metadata are provided by the [World Bank API](https://datahelpdesk.worldbank.org/knowledgebase/articles/889392).

Users should consult the World Bank’s indicator definitions and revision notes before using exported data in formal analysis.

## License

This project is released under the [Apache License 2.0](LICENSE).
