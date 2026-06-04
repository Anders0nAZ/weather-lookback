# Weather Look-Back

Single-page static web app showing weather for a date and the days leading up to
it across the last several years, so you can see what a given week of the year
typically looks like.

**Live:** https://anders0naz.github.io/weather-lookback/
**Repo:** https://github.com/Anders0nAZ/weather-lookback

## Architecture

The entire app is **one file: `index.html`** — inline `<style>` and inline
`<script>`, no framework, no build step, no backend, no API keys, no
dependencies. Editing means editing that one file. `README.md` is user-facing
docs.

All data is fetched client-side from free, key-less APIs. Both APIs allow CORS,
so the page works opening `index.html` directly (`file://`) — a server is only a
convenience.

## Key functions (in `index.html`)

| Function | Role |
|----------|------|
| `initDate()` | IIFE that defaults the date input to today |
| `resolveLocation(input)` | Geocodes a 5-digit US zip (zippopotam.us) or place name (Open-Meteo geocoding) → lat/lon |
| `windowFor(year, month, day, days)` | Computes the start/end date range for one year's row |
| `fetchJson(url, attempt)` | Fetch with exponential backoff on HTTP 429 |
| `runPool(items, worker, concurrency)` | Limited-concurrency request pool (respects free-tier rate limits) |
| `fetchWindow(lat, lon, start, end)` | Fetches one year's daily weather window |
| `wcIcon(code)` | Maps WMO `weather_code` → condition emoji |
| `renderForecast(years, cols, data)` | Builds the CSS-grid matrix (dates as columns, years as rows) |
| `$("f").addEventListener("submit", ...)` | Main entry point wiring the form to fetch + render |

`const $ = id => document.getElementById(id)` is the only DOM helper.

## Data sources (all free, no key, non-commercial + attribution)

- **Geocoding:** zippopotam.us (US zips), Open-Meteo Geocoding API (place names).
- **Weather — historical:** Open-Meteo Historical Weather API (ERA5 reanalysis).
- **Weather — recent (~within 6 days of today):** Open-Meteo Forecast API,
  because the historical archive lags real time by a few days. These cells may
  be partly forecast rather than observed.

## Units & conventions

- Temps in **°F**, precipitation in **inches**.
- Raindrop/wet styling only on days with measurable precip (>= 0.01").
- Today's cell is highlighted (`.cell.today`).
- Layout is a CSS grid (`.matrix`): column headers = dates, sticky left column
  (`.rowhead`) = years. The sticky year column has known fiddly shadow/padding
  behavior on horizontal scroll — recent commits fixed sliver/bleed-through
  artifacts, so test scrolling on narrow screens after touching `.matrix`,
  `.corner`, or `.rowhead` styles.

## Caveats

- Historical values are **ERA5 gridded reanalysis**, not station observations —
  they reflect the grid cell, won't match official record books.
- Open-Meteo free tier is **non-commercial** and requires attribution (in the
  page footer — keep it).

## Deployment

GitHub Pages from the **`main` branch root**. Pushing to `main` auto-rebuilds
within ~1 minute. There is no CI, no test suite — verify changes by opening
`index.html` in a browser (or `python -m http.server 8765`).
