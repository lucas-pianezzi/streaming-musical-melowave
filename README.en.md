# Analyzing and anticipating music streaming trends (MeloWave)

[🇫🇷 Français](README.md) · 🇬🇧 English

Project 9 of the **Data Analyst** program (OpenClassrooms): using Spotify data, I audit a music catalog, statistically test three editorial hypotheses, then forecast how often Pop appears in the global Top 200 using Prophet.

📄 **[View the presentation (PDF, 18 slides, in French)](presentation_melowave.pdf)**

## Business context

The fictional platform **MeloWave** (a training scenario) asks its Head of Music Research to support its editorial decisions:

- **Mission 1 – Catalog**: is the catalog reliable, and what do the data say about audio features, popularity and explicit content?
- **Mission 2 – Forecast**: how is Pop's presence in the global Top 200 evolving, and what can be anticipated over the next 6 months?

## Key results

### Mission 1 – Audit and statistical tests

| Committee question | Test (why) | Result | Business takeaway |
|---|---|---|---|
| Are audio features related? (`energy` / `loudness`) | Spearman (non-normal distributions) | ρ = 0.753, p < 10⁻¹⁵ | Energetic tracks are mastered louder: pick high-loudness tracks for "Workout" playlists |
| Does popularity differ by genre? | Kruskal-Wallis (normality rejected) | H = 30,955, p < 10⁻¹⁵ | Don't treat all genres alike: target genres with high streaming potential |
| Does explicit content depend on genre? | Chi-squared independence test | χ² ≈ 18,362, p < 10⁻¹⁵ | Filter explicit content upstream for general-audience playlists |

Documented cleaning: 114,000 rows at the start, 113,549 track-genre pairs kept, 89,740 unique tracks (duplicate `track_id`s are multi-genre tracks, not errors). Outliers (IQR) are kept because they reflect the real diversity of the catalog.

### Mission 2 – Pop forecast (global Top 200, 2017-2020)

- **Series rebuilt on a complete date grid**: 5 zero-count days, all identified as data-collection gaps (not true zeros) by comparing with the global market series.
- **Trend**: Pop is declining, from about 87 tracks in 2017 to about 73 at the end of 2020.
- **Yearly seasonality**: peak in December (+9 tracks), dips in early July and early October (about −6).
- **Forecast quality** (last 90 days held out): MAE 17.8 tracks/day, RMSE 19.0, MAPE 23.3%. The model systematically underestimates, so the 6-month forecast should be read as a **direction**, not an exact level.
- **Monthly release pace**: unreliable forecast (MAPE 20.8%), weakened by an end-of-period bias (tracks released late 2020 had little time to reach the charts).

## Limitations

- The forecast assumes past patterns continue; reliability decreases with the horizon.
- The data stop in November 2020.
- Spotify popularity reflects recent plays: a score of 0 (10.5% of the catalog) often means a dormant track, not an uninteresting one.

## Repository content

| File | Purpose |
|---|---|
| `analyse_catalogue_melowave.ipynb` | Mission 1: data quality audit, audio profiling, statistical tests |
| `prevision_activite.ipynb` | Mission 2: time series, Prophet, release pace |
| `presentation_melowave.pdf` | Presentation slides (in French) |
| `requirements.txt` | Required Python libraries |

Note: the notebooks are written in French.

## Reproduce the analysis

1. Install Python 3.10 or newer.
2. Create an environment and install the libraries:
   ```bash
   python -m venv .venv
   .venv\Scripts\activate        # Windows (Mac/Linux: source .venv/bin/activate)
   pip install -r requirements.txt
   ```
3. Put the three data files in the same folder as the notebooks (not published here: they were provided as part of the OpenClassrooms project):
   - `spotify-tracks-dataset-detailed.csv`
   - `chart_tracks_genre_2017_2020.csv`
   - `release_dates_genre_2017_2020.csv`
4. Run `jupyter notebook` and execute the cells in order. Charts are already visible in the notebooks on GitHub, no need to run anything.

## Tools

Python · pandas · NumPy · SciPy · statsmodels · scikit-learn · Prophet · Matplotlib · Seaborn · Jupyter

## Author

**Lucas Pianezzi** — looking for a Data Analyst position.
