# CrimeWatch Analytics

A production-grade, AI-powered crime analysis and intelligence dashboard built with HTML, CSS, JavaScript, Chart.js, and Claude AI integration.

---

## Overview

CrimeWatch Analytics is a unified crime intelligence platform designed for law enforcement agencies, analysts, and administrators. It provides real-time dashboards, incident management, predictive forecasting, and AI-driven insights — all in a single, responsive web interface.

---

## Features

### Dashboard
- Summary metrics: total incidents, clearance rate, high-risk zones, average response time
- Monthly crime trend line chart (multi-series by crime type)
- Crime type distribution doughnut chart
- Time-of-day vs day-of-week heatmap
- District-level comparison bar chart (resolved vs open cases)
- Auto-generated AI insight cards

### Incidents
- Searchable and sortable incident table (50+ records)
- Fields: Incident ID, Date, Crime Type, Location, Severity, Status, Assigned Officer
- Color-coded severity and status badges
- AI-powered pattern analysis via one-click prompt

### Predict
- 30-day crime forecast chart with confidence bands
- Geographic risk zone dot-map with density visualization
- Per-crime-type prediction cards with confidence scores
- Prevention strategy generator via AI

### AI Analysis Center
- Six pre-built AI analysis prompts:
  - Crime hotspot identification
  - Year-over-year statistical comparison
  - Resource allocation analysis
  - Temporal crime pattern analysis
  - Executive briefing report generation
  - Quarterly crime prediction
- Model performance metrics (accuracy, precision, false positive rate)

---

## Tech Stack

| Layer | Technology |
|---|---|
| UI Framework | HTML5, CSS3 (custom design system) |
| Charts | Chart.js 4.4.1 |
| AI Integration | Anthropic Claude API (claude-sonnet-4) |
| Styling | CSS Variables, responsive grid layout |
| Runtime | Vanilla JavaScript (no build step required) |

---

## Project Structure

```
crimewatch-analytics/
├── index.html              # Main application entry point
├── README.md               # This file
├── assets/
│   ├── css/
│   │   └── styles.css      # Global styles and design tokens
│   └── js/
│       ├── charts.js       # Chart.js chart builders
│       ├── table.js        # Incident table logic
│       ├── predict.js      # Forecast and risk map
│       ├── filters.js      # Sidebar filter logic
│       └── ai.js           # Claude API integration
├── data/
│   └── sample_incidents.json   # Sample incident dataset
└── python/
    ├── requirements.txt        # Python dependencies
    ├── data_processor.py       # Data cleaning and aggregation
    ├── crime_model.py          # Predictive model (scikit-learn)
    ├── hotspot_detector.py     # Clustering-based hotspot detection
    └── api_server.py           # FastAPI backend server
```

---

## Python Backend

The platform includes an optional Python backend for real data processing and ML-based predictions.

### Requirements

```
fastapi>=0.110.0
uvicorn>=0.29.0
pandas>=2.2.0
numpy>=1.26.0
scikit-learn>=1.4.0
matplotlib>=3.8.0
seaborn>=0.13.0
geopandas>=0.14.0
prophet>=1.1.5
python-dotenv>=1.0.0
```

Install dependencies:

```bash
pip install -r python/requirements.txt
```

### Running the API Server

```bash
cd python
uvicorn api_server:app --reload --port 8000
```

API will be available at `http://localhost:8000`. Interactive docs at `http://localhost:8000/docs`.

### Key API Endpoints

| Method | Endpoint | Description |
|---|---|---|
| GET | `/incidents` | Fetch all incidents (with optional filters) |
| GET | `/incidents/{id}` | Fetch single incident by ID |
| GET | `/metrics/summary` | Summary metrics for dashboard |
| GET | `/metrics/trend` | Monthly trend data |
| GET | `/metrics/heatmap` | Hour vs day heatmap data |
| GET | `/districts` | Per-district statistics |
| GET | `/predict/forecast` | 30-day forecast with confidence bands |
| GET | `/predict/hotspots` | Geographic hotspot clusters |
| POST | `/incidents` | Create a new incident record |

### Data Processing (`data_processor.py`)

Handles ingestion and cleaning of raw crime data:

- Parses CSV/JSON incident exports from common CAD (Computer-Aided Dispatch) systems
- Normalizes crime type classifications across sources
- Computes rolling averages, clearance rates, and seasonal indices
- Outputs clean dataframes ready for model input

### Predictive Model (`crime_model.py`)

Uses Facebook Prophet and scikit-learn for forecasting:

- Time-series forecasting per crime type (Prophet)
- Severity classification (Random Forest)
- Feature engineering: day of week, hour, district, weather proxy, seasonality
- Returns point predictions + 80% and 95% confidence intervals

### Hotspot Detection (`hotspot_detector.py`)

Spatial clustering using DBSCAN:

- Groups incident coordinates into density-based clusters
- Assigns risk scores based on recency, frequency, and severity
- Outputs GeoJSON for map rendering
- Supports configurable epsilon and min-samples for different city densities

---

## Getting Started

### Option 1 — Frontend only (no backend)

Open `index.html` directly in any modern browser. All charts use randomized sample data. AI prompts connect to Claude via the embedded API integration.

### Option 2 — Full stack with Python backend

1. Clone the repository:

```bash
git clone https://github.com/your-org/crimewatch-analytics.git
cd crimewatch-analytics
```

2. Set up Python environment:

```bash
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install -r python/requirements.txt
```

3. Configure environment variables:

```bash
cp .env.example .env
# Edit .env and add your Anthropic API key and database connection string
```

4. Start the backend:

```bash
cd python
uvicorn api_server:app --reload
```

5. Open `index.html` in your browser (or serve via a local HTTP server):

```bash
python -m http.server 3000
# Then open http://localhost:3000
```

---

## Configuration

Create a `.env` file in the project root:

```env
ANTHROPIC_API_KEY=your_api_key_here
DATABASE_URL=postgresql://user:password@localhost/crimewatch
DATA_SOURCE=sample          # 'sample' | 'database' | 'csv'
CSV_PATH=./data/incidents.csv
MODEL_RETRAIN_INTERVAL_DAYS=7
```

---

## Filters and Customization

The sidebar exposes four filters that dynamically update all charts:

- **Time Period** — 30 days, 90 days, 6 months, 12 months
- **District** — North, South, East, West, Central Zone (or all)
- **Crime Type** — Theft, Assault, Burglary, Fraud, Drug, Vandalism (multi-select)
- **Severity** — High, Medium, Low (multi-select)

To add custom districts or crime types, update the `DISTRICTS` and `CRIME_TYPES` arrays in the JavaScript, and the corresponding filter choices in the HTML sidebar.

---

## AI Integration

The platform uses the Anthropic Claude API to power the AI Analysis center and insight cards. The API is called directly from the browser using `fetch`:

```javascript
const response = await fetch("https://api.anthropic.com/v1/messages", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({
    model: "claude-sonnet-4-20250514",
    max_tokens: 1000,
    messages: [{ role: "user", content: prompt }]
  })
});
```

All six AI prompts in the Analysis Center are pre-written and fire directly into a Claude conversation for instant results.

---

## Data Format

Sample incident JSON structure:

```json
{
  "id": "INC-1042",
  "date": "2025-03-15",
  "time": "23:14",
  "type": "Theft",
  "subtype": "Vehicle Theft",
  "location": "Main St & 5th Ave",
  "latitude": 12.9716,
  "longitude": 77.5946,
  "severity": "High",
  "status": "Under Investigation",
  "officer": "Ofc. Sharma",
  "district": "North Zone",
  "notes": "Suspect fled on foot heading east"
}
```

---

## Browser Compatibility

| Browser | Supported |
|---|---|
| Chrome 110+ | Yes |
| Firefox 115+ | Yes |
| Safari 16+ | Yes |
| Edge 110+ | Yes |
| Mobile Chrome/Safari | Yes (responsive) |

---

## Roadmap

- Real-time incident feed via WebSocket
- Live map integration with Leaflet.js and OpenStreetMap
- Export to PDF and CSV
- Multi-user authentication and role-based access
- Integration with national crime databases (NCRB API)
- Automated daily briefing emails via SendGrid
- Mobile app (React Native)

---

## License

MIT License. See `LICENSE` for full terms.

---

## Contributing

Pull requests are welcome. For major changes, open an issue first to discuss what you'd like to change. Please ensure all new features include appropriate sample data and documentation updates.

---

## Contact

Built with Claude AI by Anthropic. For questions or enterprise licensing, open an issue on GitHub.
