# Weather Look-Back

A single-page web app that shows the weather for a given date and the days
leading up to it, across the last several years — so you can see what a
particular week of the year has typically looked like.

**Live:** https://anders0naz.github.io/weather-lookback/

For example, entering **5/28/2026** with a 7-day window and 10 years back shows
the weather for **May 22–28** in each year from **2017 through 2026**.

## Features

- Enter a **date** (defaults to today) and a **US zip code** or a **place name**
  (e.g. `90210` or `Denver, CO`).
- Adjustable **window length** (days, including the date) and **years back**.
- Results are laid out as a grid: dates run across the top, years down the left.
- Each cell shows a **condition icon**, **high °F**, **low °F**, and
  **precipitation (in)**. Today's date is highlighted.
- No build step, no backend, no API key — it's one static HTML file.

## Usage

Just open the live URL, or open `index.html` locally (see below). Fill in the
date and location, optionally tweak the day/year counts, and click
**Get weather**.

- Temperatures are in **Fahrenheit**, precipitation in **inches**.
- A raindrop appears only on days with measurable precipitation (>= 0.01").

## Running locally

Because both data APIs allow cross-origin browser requests, no server is
required — you can simply double-click `index.html` to open it in a browser
(an internet connection is still needed to fetch the weather data).

To serve it instead:

```sh
python -m http.server 8765
# then visit http://localhost:8765
```

## How it works

All data is fetched client-side from free, key-less APIs:

- **Geocoding**
  - 5-digit US zip codes are resolved via [zippopotam.us](https://www.zippopotam.us/).
  - Place names are resolved via the
    [Open-Meteo Geocoding API](https://open-meteo.com/en/docs/geocoding-api).
- **Weather**
  - Older dates use the
    [Open-Meteo Historical Weather API](https://open-meteo.com/en/docs/historical-weather-api)
    (ERA5 reanalysis).
  - Dates within ~6 days of today use the
    [Open-Meteo Forecast API](https://open-meteo.com/en/docs), since the archive
    lags real time by a few days. These values may be partly forecast.
- Each year's window is fetched as a separate request, with limited concurrency
  and exponential backoff on HTTP 429 to stay within the free rate limits.
- Condition icons are derived from the daily WMO `weather_code`.

## Caveats

- Historical values come from **ERA5 gridded reanalysis**, not official station
  observations, so they reflect the local grid cell rather than a specific
  weather station and will not match official record books.
- Open-Meteo's free tier is for **non-commercial use** and requires attribution
  (credited in the page footer).

## Deployment

Hosted on **GitHub Pages** from the `main` branch (root folder). Pushing to
`main` triggers an automatic rebuild within about a minute.
