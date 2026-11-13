# TP2 — Accidents de la route — PySpark

**Fadwa Haddad · Identifiant : 05** · Cours de Big Data (école d'ingénieurs, 5e année)

Reproduction, vérification et extension de l'article *Big data Analysis of Road Crash Data using PySpark* (R. Nain, 2021), sur les accidents de la route en Australie-Méridionale (2012-2019, environ 128 000 accidents et 273 000 véhicules ou usagers, 77 Mo).

Le notebook [`road_crash_analyse.ipynb`](road_crash_analyse.ipynb) est livré exécuté. Sa première cellule résume la contribution (exécution de bout en bout vérifiée, problèmes rencontrés, modifications), et chaque section est précédée d'une étiquette d'étape (`## [Data]`, `## [EDA]`, `## [Prétraitement]`, `## [Passage à l'échelle]`, `## [Conclusion]`).

## Démarche

1. **Reproduire** les résultats de l'article avec les RDD et les DataFrames : ils sont retrouvés à l'identique avec Spark 3.5.
2. **Vérifier et corriger** les erreurs de l'article.
3. **Contribuer** : performance de Spark contre pandas, format Parquet, analyse complémentaire.

## Ce qui a été corrigé

| Problème de l'article | Ampleur mesurée | Correction |
|---|---|---|
| Découpage des CSV par `split(',')` dans les RDD | 16,7 % des accidents mal découpés | module `csv`, ou lecture en DataFrame |
| Requêtes « équivalentes » en RDD, DataFrame et SQL | résultats incompatibles (Adélaïde : 3 935 contre 7 514) | requêtes réellement équivalentes, vérifiées entre elles |
| Somme d'une colonne de l'accident après jointure | 2,17 fois trop de victimes | dédoublonner avant d'additionner |
| Colonne annoncée « `Lic State` » | c'est en réalité `Veh Reg State` | colonnes désignées par leur nom |
| Qualité des données | 19 % d'années de véhicule manquantes, valeur par défaut « 1900 » | nettoyage et filtrage |

## Ce qui a été ajouté

- **Performance** : sur les 77 Mo réels, Spark est 2,3 fois plus lent que pandas (1,07 s contre 0,46 s). Même à 40 fois ce volume (1,6 Go), pandas reste du même ordre que Spark. Le format **Parquet** accélère la même requête Spark d'un facteur 9,9 par rapport au CSV.
- **Analyse** : à 100 ou 110 km/h, un accident est environ 5 fois plus souvent grave ou mortel qu'à 50 ou 60 km/h ; la nuit, 2 à 3 fois plus qu'en journée. Ce sont des associations, pas des effets causaux isolés.

## Exécution

Les fichiers de l'auteur ne sont pas redistribués :

```bash
git clone --depth 1 https://github.com/RakeshNain/Big-data-Analysis-of-Road-Crash-Data-using-PySpark-with-PySpark-Tutorial.git source_auteur
```

Adapter ensuite la variable `PROJET` de la première cellule de code au dossier qui contient `source_auteur/` et le notebook. Environnement : Python 3.10, PySpark 3.5.9, Java 11, pandas 2.3. Exécution complète : environ 6 min 30 sur un portable (12 threads).

Les mesures de temps varient d'une exécution à l'autre. Lors de la réexécution de contrôle du 13/11/2026, les résultats d'analyse étaient identiques ; seuls les temps différaient légèrement.
