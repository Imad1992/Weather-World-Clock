# Weather + World Clock Dashboard

A single-page web app (pure **HTML/CSS/JavaScript**) that combines:

- A **Weather Dashboard** (current conditions + hourly chart + 7‑day forecast)
- A **World Clock Bar** (multiple time zones updating every second)

It uses the public **Open‑Meteo API** (no API key required) and stores user preferences in **LocalStorage**, so your last city and time zones persist after refresh.

---

## Demo / What you get

### Weather Dashboard
- Search a city (example: `Boston`, `Casablanca`, `Dubai`)
- Fetch:
  - **Current** temperature, feels-like, weather description
  - **Wind** speed + direction
  - **Humidity**
  - **Precipitation probability** (from hourly data, near “now”)
  - **Sunrise / Sunset** (daily)
- Display:
  - **24-hour temperature chart** (Canvas line chart)
  - **7-day forecast** (min/max + icon per day)

### World Clock
- Displays multiple clocks in a bar
- Updates **every second**
- Add a time zone from a dropdown list
- Add **Local** time zone (browser-detected)
- Remove any time zone
- Saves the clock list so it stays after reloading

---

## How it works (high-level)

### 1) Weather flow
Weather is fetched in **two steps**:

1. **Geocoding (City name → coordinates)**
   - API: Open‑Meteo Geocoding
   - Input: city name typed by the user
   - Output: latitude, longitude, timezone

2. **Forecast/Current Weather (Coordinates → weather data)**
   - API: Open‑Meteo Forecast endpoint
   - Input: latitude, longitude, timezone
   - Output: current + hourly + daily weather fields

The app then:
- renders the current summary card
- draws the next **24 hours** temperature chart
- renders the **7-day** forecast tiles

### 2) World clock flow
The clock does **not** call any API. It uses the browser’s date formatting:

- `Intl.DateTimeFormat(..., { timeZone: "America/New_York", ... })`

A timer runs every second:
- reads the current `Date()`
- formats the time/date for each stored time zone
- updates the DOM

### 3) LocalStorage persistence
The app stores settings locally in the browser:

- **Clocks list** (time zones you added/removed)
- **Last used city** (the last location you loaded)
- **Unit preference** (Metric/Imperial)

This means:
- open the site again → your previous setup is restored automatically

---

## Features (full list)

### Weather features
- City search (by name)
- Uses Open‑Meteo (no key)
- Current conditions:
  - temperature
  - feels-like temperature
  - WMO weather code → readable text
  - wind speed + compass direction
  - humidity
  - precip probability (approx. “now”)
  - sunrise / sunset
- 24-hour temperature chart (Canvas)
- 7-day forecast (min/max + icon)
- “Use my location” (browser geolocation) *(if enabled)*
- Metric / Imperial toggle:
  - °C ↔ °F
  - km/h ↔ mph

### World clock features
- Add time zones from dropdown
- Add Local time zone automatically
- Remove time zones
- Live updates every second
- Saved to LocalStorage

### UI/UX features
- Responsive layout (works on mobile and desktop)
- Fast (no framework, just one HTML file)

---

## APIs used

### Open‑Meteo Geocoding API
- Purpose: city name → coordinates + timezone
- Endpoint:
  - `https://geocoding-api.open-meteo.com/v1/search?name=...`

### Open‑Meteo Forecast API
- Purpose: current + hourly + daily weather data
- Endpoint:
  - `https://api.open-meteo.com/v1/forecast?...`

Requested fields (typical):
- `current`: temperature, apparent temperature, wind, humidity, weather code
- `hourly`: temperature, precipitation_probability
- `daily`: sunrise, sunset, min/max temp, weather code

No authentication required.

---

## Project structure

This project can be a single file:

```text
.
├── index.html
└── README.md
```

Optional (recommended) structure if you later expand:

```text
.
├── index.html
├── assets/
│   └── screenshot.png
└── README.md
```

---

## How to run locally

### Option A: Open directly
1. Download/clone the repo
2. Double-click `index.html`

> Note: Most browsers allow `fetch()` from HTTPS endpoints even when the file is opened directly.
> If your browser blocks requests, use Option B.

### Option B: Run a local server (recommended)
**Python**
```bash
python -m http.server 8000
```

Then open:
- `http://localhost:8000`

**Node (if you have it)**
```bash
npx serve .
```

---

## How to deploy for free (GitHub Pages)

### 1) Create a repository
- Create a new GitHub repo, e.g. `weather-world-clock`
- Add `index.html` and `README.md`
- Push to GitHub

### 2) Enable GitHub Pages
1. Go to **Settings → Pages**
2. Source: **Deploy from a branch**
3. Branch: `main` and folder `/ (root)`
4. Save

After a minute, GitHub will show the public URL.

---

## Configuration & customization

### Add/remove time zones in the dropdown
Edit the `ZONE_CHOICES` list in `index.html`:
```js
const ZONE_CHOICES = [
  { label: "UTC", tz: "UTC" },
  { label: "New York", tz: "America/New_York" },
  // ...
];
```

### Change default clocks
Edit the initial `clocks` array used when LocalStorage is empty:
```js
let clocks = loadClocks() || [
  { label: "Local", tz: resolvedLocalTZ() },
  { label: "UTC", tz: "UTC" },
  { label: "New York", tz: "America/New_York" }
];
```

### Change unit defaults
The app stores units in LocalStorage. If you want default imperial, set:
```js
let useImperial = true;
```

---

## Notes / Limitations

- **Precipitation probability**: the app takes the first hourly value as “near now”.
  For higher accuracy, you can match `hourly.time[]` to the current time and pick the closest index.
- **Geolocation** depends on browser permission. If denied, the app can’t fetch local weather.
- The WMO weather descriptions are a simplified mapping (good for dashboards).

---

## Roadmap ideas (nice upgrades)
- Multiple saved weather locations (tabs/cards)
- Alerts/notifications (rain/wind/temp thresholds)
- Meeting planner (choose a time and convert across all zones)
- Offline caching (PWA) + last-known weather display
- Auto-refresh weather every X minutes with a “stale” indicator

---

## License
Add a license if you plan to share publicly (MIT is common).
