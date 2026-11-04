# Analyser et anticiper les tendances du streaming musical (MeloWave)

🇫🇷 Français · [🇬🇧 English](README.en.md)

Projet 9 de la formation **Data Analyst** (OpenClassrooms) : à partir de données Spotify, j'audite un catalogue musical, je valide statistiquement trois hypothèses éditoriales, puis je prévois la présence de la Pop dans le Top 200 mondial avec Prophet.

📄 **[Voir la présentation (PDF, 18 diapositives)](presentation_melowave.pdf)**

## Contexte business

La plateforme fictive **MeloWave** (mise en situation de la formation) demande à sa directrice des études musicales d'éclairer ses choix éditoriaux :

- **Mission 1 – Catalogue** : le catalogue est-il fiable, et que montrent les données sur les caractéristiques audio, la popularité et le contenu explicite ?
- **Mission 2 – Prévision** : comment évolue la présence de la Pop dans le Top 200 mondial, et que peut-on anticiper sur 6 mois ?

## Résultats clés

### Mission 1 – Audit et tests statistiques

| Question du comité | Test choisi (pourquoi) | Résultat | Conclusion business |
|---|---|---|---|
| Les caractéristiques audio sont-elles liées ? (`energy` / `loudness`) | Spearman (distributions non normales) | ρ = 0,753, p < 10⁻¹⁵ | Les morceaux énergiques sont masterisés plus fort : sélectionner des titres à fort volume pour les playlists « Workout » |
| La popularité diffère-t-elle selon le genre ? | Kruskal-Wallis (normalité rejetée) | H = 30 955, p < 10⁻¹⁵ | Ne pas traiter tous les genres pareil : cibler les genres à fort potentiel de streaming |
| Le contenu explicite dépend-il du genre ? | χ² d'indépendance | χ² ≈ 18 362, p < 10⁻¹⁵ | Filtrer le contenu explicite en amont pour les playlists grand public |

Nettoyage documenté : 114 000 lignes au départ, 113 549 associations morceau-genre conservées, 89 740 morceaux uniques (les doublons de `track_id` correspondent à des morceaux multi-genres, ce n'est pas une erreur). Les valeurs aberrantes (IQR) sont conservées car elles reflètent la diversité réelle du catalogue.

### Mission 2 – Prévision de la Pop (Top 200 mondial, 2017-2020)

- **Série reconstruite sur une grille de dates complète** : 5 jours à zéro, tous identifiés comme des trous de collecte (et non de vrais zéros) en les comparant à la série du marché global.
- **Tendance** : la Pop recule, d'environ 87 titres en 2017 à environ 73 fin 2020.
- **Saisonnalité annuelle** : pic en décembre (+9 titres), creux début juillet et début octobre (environ −6).
- **Qualité de la prévision** (test sur les 90 derniers jours) : MAE 17,8 titres/jour, RMSE 19,0, MAPE 23,3 %. Le modèle sous-estime systématiquement : la prévision à 6 mois se lit comme une **direction**, pas comme un niveau exact.
- **Rythme de sortie mensuel** : prévision peu fiable (MAPE 20,8 %), fragilisée par un biais de fin de période (les titres sortis fin 2020 n'ont pas eu le temps d'entrer dans les classements).

## Limites

- La prévision suppose que les motifs passés se poursuivent ; sa fiabilité diminue avec l'horizon.
- Les données s'arrêtent en novembre 2020.
- La popularité Spotify reflète les écoutes récentes : un score de 0 (10,5 % du catalogue) signale souvent un titre dormant, pas un titre sans intérêt.

## Contenu du dépôt

| Fichier | Rôle |
|---|---|
| `analyse_catalogue_melowave.ipynb` | Mission 1 : audit qualité, profil audio, tests statistiques |
| `prevision_activite.ipynb` | Mission 2 : série temporelle, Prophet, rythme de sortie |
| `presentation_melowave.pdf` | Support de présentation (soutenance) |
| `requirements.txt` | Bibliothèques Python nécessaires |

## Reproduire l'analyse

1. Installer Python 3.10 ou plus récent.
2. Créer un environnement et installer les bibliothèques :
   ```bash
   python -m venv .venv
   .venv\Scripts\activate        # Windows (Mac/Linux : source .venv/bin/activate)
   pip install -r requirements.txt
   ```
3. Placer les trois fichiers de données dans le même dossier que les notebooks (ils ne sont pas publiés ici : ils ont été fournis dans le cadre du projet OpenClassrooms) :
   - `spotify-tracks-dataset-detailed.csv`
   - `chart_tracks_genre_2017_2020.csv`
   - `release_dates_genre_2017_2020.csv`
4. Lancer `jupyter notebook` et exécuter les cellules dans l'ordre. Les graphiques sont déjà visibles dans les notebooks sur GitHub, sans rien exécuter.

## Outils

Python · pandas · NumPy · SciPy · statsmodels · scikit-learn · Prophet · Matplotlib · Seaborn · Jupyter

## Auteur

**Lucas Pianezzi** — en recherche d'un poste de Data Analyst.
