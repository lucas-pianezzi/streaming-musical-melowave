[Français](README.md) | **English**

# Catalog Audit, Statistical Tests & Forecasting (MeloWave)

## At a glance

MeloWave, a music streaming platform, wants to support its editorial decisions. I audited its Spotify catalog, statistically tested three hypotheses from the editorial committee (Mission 1), then forecast Pop's presence in the global Top 200 with Prophet (Mission 2).

- **Tools:** Python, pandas, SciPy, statsmodels, scikit-learn, Prophet, Matplotlib, Seaborn, Jupyter
- **Volume:** 114,000 rows (89,740 unique tracks), daily charts 2017-2020
- **Deliverables:** 2 notebooks, results presentation ([PDF, in French](presentation_melowave.pdf))

---

## Data quality

- **Missing values:** only on text fields (< 0.001% of rows); rows dropped with no bias on the audio analyses.
- **Duplicates:** two working datasets. `df_genres` keeps every track-genre pair (113,549 rows); `df_tracks` is deduplicated on `track_id` (89,740 tracks). The same track can legitimately belong to several genres.
- **Outliers (IQR):** duration (4.7%), loudness (5.6%), tempo (0.6%). Kept, since they reflect the real diversity of the catalog rather than errors.
- **Time series:** complete date grid. The 5 zero-count days for Pop are data-collection gaps (not true zeros), checked against the global market series, then set to missing.

## Key findings

### Mission 1: statistical tests

| Committee question | Test (why) | Result | Business takeaway |
|---|---|---|---|
| Are audio features related? (`energy` / `loudness`) | Spearman (non-normal distributions) | ρ = 0.753, p < 10⁻¹⁵ | Energetic tracks are mastered louder: aim for high-loudness tracks for "Workout" playlists |
| Does popularity differ by genre? | Kruskal-Wallis (normality rejected) | H = 30,955, p < 10⁻¹⁵ | Don't treat all genres alike: target those with high streaming potential |
| Does explicit content depend on genre? | Chi-squared independence test | χ² ≈ 18,362, p < 10⁻¹⁵ | Filter explicit content upstream for general-audience playlists |

### Mission 2: Pop forecast (global Top 200)

| Finding | Key figure |
|---|---|
| Pop is declining in the Top 200 | from about 87 tracks in 2017 to about 73 at the end of 2020 |
| Strong yearly seasonality | peak in December (+9 tracks), dips in early July and early October (about −6) |
| Weak weekly effect | about −2 tracks on Fridays and Saturdays |
| Model quality (last 90 days held out) | MAE 17.8 tracks/day, RMSE 19.0, MAPE 23.3% |
| Monthly release pace hard to predict | MAPE 20.8% |

## A critical look at the data

- **Read the forecast as a trend:** the model systematically underestimates Pop on the test period. The 6-month forecast shows a direction and favorable periods, not an exact level. Its reliability decreases with the horizon.
- **End-of-period bias:** the file only lists tracks that entered the charts and collection stops in November 2020. Tracks released in late 2020 are undercounted, which weakens the release-pace forecast.
- **Popularity of 0:** 10.5% of the catalog. Spotify's indicator reflects recent plays, so a zero often means a dormant track, not an uninteresting one.

## Repository contents

| File | Description |
|---|---|
| `analyse_catalogue_melowave.ipynb` | Mission 1: data quality audit, audio profiling, statistical tests |
| `prevision_activite.ipynb` | Mission 2: time series, Prophet, release pace |
| `presentation_melowave.pdf` | Results presentation (in French) |
| `requirements.txt` | Python libraries |
| `README.md` / `README.en.md` | Documentation in French / English |

The notebooks are written in French.

## Reproduce the analysis

1. Create an environment and install the libraries: `pip install -r requirements.txt`
2. Put the three data files in the notebooks folder (not published here): `spotify-tracks-dataset-detailed.csv`, `chart_tracks_genre_2017_2020.csv`, `release_dates_genre_2017_2020.csv`
3. Run `jupyter notebook` and execute the cells in order. Charts are already visible in the notebooks on GitHub.

---

Project completed as part of the OpenClassrooms Data Analyst program.
