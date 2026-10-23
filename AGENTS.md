# AGENTS.md

Guide pour les agents de code travaillant sur ce dépôt. Voir aussi `CLAUDE.md`, qui contient le même contexte en plus détaillé.

## Projet

« Mon Niko Niko » : tableau de bord personnel d'humeur hébergé sur GitHub Pages. Application statique sans dépendance, un seul fichier (HTML + CSS + JS vanilla), inspirée du calendrier Niko Niko.

## Développement

Pas de build, pas de gestionnaire de paquets, pas de tests ni de linter. Toute l'application est dans `index.html`.

```bash
python -m http.server 8000   # puis http://localhost:8000/index.html
```

Déploiement automatique via GitHub Pages au push sur `master`.

## Architecture

- **`index.html`** : classe `NikoNiko` qui charge les humeurs, calcule les stats et affiche : l'humeur du jour, l'évolution (graphique, stats rouge/vert), le baromètre, le calendrier, l'Ikigai.
- **`moods/<année>.json`** : données, une entrée par jour `YYYY-MM-DD` avec `value` (1-5), `reason` (emoji, optionnel) et `vacation` (booléen, optionnel). Une entrée par ligne (JSON compact). Pour une nouvelle année, créer `moods/<année>.json` avec `{"moods": {}}` : la page charge toutes les années de `START_YEAR` à l'année en cours.
- **`ikigai.json`** : Ikigai du moment, sans historique. Quatre booléens `aime`, `doue`, `besoin`, `paye`. Le fichier est simplement réécrit à chaque mise à jour.
- **`EMOJI_INTERPRETATIONS`** (dans `index.html`) : explications des emoji de raison. Tout nouvel emoji de raison utilisé dans les données doit y être ajouté.

Mise en page : sur ordinateur (≥ 1100 px), deux colonnes `.col-now` (moment présent) et `.col-history` (historique). Sur mobile, l'ordre des cartes est fixé par `order` en CSS : en ajoutant une carte, lui donner sa place dans les deux.

Échelle d'humeur : 1 (😢 Difficile) → 5 (😄 Excellent).

## Règles de calcul à respecter

- **Rouge / vert** (`displayTimeline()`) : la courbe de tendance (moyenne mobile sur 7 jours) sous / au-dessus du niveau 3, pas la note brute du jour.
- **Baromètre** (`displayForecast()`) : tendance des 3 prochains jours = moyenne du jour de la semaine + écart de forme des 7 derniers jours (estompé de 40 % par jour). Volontairement simple : on lit une direction, pas une valeur. Masqué sous 28 jours de données.

## Conventions

- Interface et README en **français**, avec les accents.
- Commits d'humeur : `🎭 Humeur du JJ/MM : [emoji]`.
- Commits d'Ikigai : `🌸 Ikigai : ❤️💪💰` (emoji des cercles à oui, `—` si aucun).
- Les 16 cas de l'Ikigai restent dans la modale « ❓ Aide », pas sur la page principale.
