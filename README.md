**Français** | [English](README.en.md)

# Audit de catalogue, tests statistiques et prévision (MeloWave)

## En bref

MeloWave, plateforme de streaming musical, veut éclairer ses choix éditoriaux. J'ai audité son catalogue Spotify, validé statistiquement trois hypothèses du comité éditorial (Mission 1), puis prévu la présence de la Pop dans le Top 200 mondial avec Prophet (Mission 2).

- **Outils :** Python, pandas, SciPy, statsmodels, scikit-learn, Prophet, Matplotlib, Seaborn, Jupyter
- **Volume :** 114 000 lignes (89 740 morceaux uniques), classements quotidiens 2017-2020
- **Livrables :** 2 notebooks, présentation des résultats ([FR](presentation_melowave.pdf) · [EN](presentation_melowave_en.pdf))

---

## Qualité des données

- **Valeurs manquantes :** uniquement sur des champs texte (< 0,001 % des lignes), lignes supprimées sans biais sur les analyses audio.
- **Doublons :** deux jeux de travail. `df_genres` garde tous les rattachements morceau-genre (113 549 lignes) ; `df_tracks` est dédupliqué sur `track_id` (89 740 morceaux). Un même titre peut légitimement appartenir à plusieurs genres.
- **Valeurs aberrantes (IQR) :** durée (4,7 %), volume sonore (5,6 %), tempo (0,6 %). Conservées, car elles reflètent la diversité réelle du catalogue et non des erreurs.
- **Série temporelle :** grille de dates complète. Les 5 jours à zéro de la Pop sont des trous de collecte (et non de vrais zéros), vérifiés en les comparant à la série du marché global, puis passés en valeur manquante.

## Principaux enseignements

### Mission 1 : tests statistiques

| Question du comité | Test (pourquoi) | Résultat | Conclusion business |
|---|---|---|---|
| Les caractéristiques audio sont-elles liées ? (`energy` / `loudness`) | Spearman (distributions non normales) | ρ = 0,753, p < 10⁻¹⁵ | Les morceaux énergiques sont masterisés plus fort : viser des titres à fort volume pour les playlists « Workout » |
| La popularité diffère-t-elle selon le genre ? | Kruskal-Wallis (normalité rejetée) | H = 30 955, p < 10⁻¹⁵ | Ne pas traiter tous les genres de la même façon : cibler ceux à fort potentiel de streaming |
| Le contenu explicite dépend-il du genre ? | χ² d'indépendance | χ² ≈ 18 362, p < 10⁻¹⁵ | Filtrer le contenu explicite en amont pour les playlists grand public |

### Mission 2 : prévision de la Pop (Top 200 mondial)

| Constat | Chiffre clé |
|---|---|
| La Pop recule dans le Top 200 | d'environ 87 titres en 2017 à environ 73 fin 2020 |
| Saisonnalité annuelle marquée | pic en décembre (+9 titres), creux début juillet et début octobre (environ −6) |
| Effet semaine faible | environ −2 titres le vendredi et le samedi |
| Qualité du modèle (test sur les 90 derniers jours) | MAE 17,8 titres/jour, RMSE 19,0, MAPE 23,3 % |
| Rythme de sortie mensuel peu prévisible | MAPE 20,8 % |

## Regard critique sur les données

- **Prévision à lire comme une tendance :** le modèle sous-estime systématiquement la Pop sur la période de test. La prévision à 6 mois indique une direction et des périodes favorables, pas un niveau exact. Sa fiabilité diminue avec l'horizon.
- **Biais de fin de période :** le fichier ne recense que les morceaux entrés dans les classements et la collecte s'arrête en novembre 2020. Les titres sortis fin 2020 sont sous-comptés, ce qui fragilise la prévision du rythme de sortie.
- **Popularité à 0 :** 10,5 % du catalogue. L'indicateur Spotify reflète les écoutes récentes, donc un zéro signale souvent un titre dormant, pas un titre sans intérêt.

## Contenu du dépôt

| Fichier | Description |
|---|---|
| `analyse_catalogue_melowave.ipynb` | Mission 1 : audit qualité, profil audio, tests statistiques |
| `prevision_activite.ipynb` | Mission 2 : série temporelle, Prophet, rythme de sortie |
| `presentation_melowave.pdf` | Présentation des résultats (français) |
| `presentation_melowave_en.pdf` | Présentation des résultats (anglais) |
| `requirements.txt` | Bibliothèques Python |
| `README.md` / `README.en.md` | Documentation en français / en anglais |

## Reproduire l'analyse

1. Créer un environnement et installer les bibliothèques : `pip install -r requirements.txt`
2. Placer les trois fichiers de données dans le dossier des notebooks (non publiés ici) : `spotify-tracks-dataset-detailed.csv`, `chart_tracks_genre_2017_2020.csv`, `release_dates_genre_2017_2020.csv`
3. Lancer `jupyter notebook` et exécuter les cellules dans l'ordre. Les graphiques sont déjà visibles dans les notebooks sur GitHub.

---

Projet réalisé dans le cadre de la formation Data Analyst d'OpenClassrooms.
