# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

"Mon Niko Niko" — a personal mood tracking dashboard hosted on GitHub Pages. It's a zero-dependency, single-file static app (HTML + CSS + vanilla JS) inspired by the Niko Niko calendar from agile practices.

## Development

No build step, no package manager, no dependencies. The entire app lives in `index.html`.

**Local development:** Open `index.html` in a browser, or serve via any HTTP server:
```bash
python -m http.server 8000
```

**Deployment:** Automatic via GitHub Pages on push to main. No CI/CD pipeline.

**No tests or linting configured.**

## Architecture

- **`index.html`** — Single-file app (~27KB) containing all HTML, CSS, and JavaScript. The JS defines a `NikoNiko` class that loads mood data, computes stats, and renders a 3-month calendar grid.
- **`ikigai.json`** — Current Ikigai, e.g. `{"aime": true, "doue": true, "besoin": false, "paye": true}`. No history: the file is simply overwritten (git keeps past versions).
- **`moods/<année>.json`** — Data store for mood entries, one file per year (e.g. `moods/2026.json`). Each entry is keyed by `YYYY-MM-DD` with `value` (1-5), optional `reason` (emoji), and optional `vacation` (boolean). Entries are written one per line (compact JSON) to keep the file easy to scroll through by hand.

**Adding a new year:** create `moods/<année>.json` with `{"moods": {}}`; `index.html` picks it up automatically (it fetches every year from `START_YEAR` through the current year — no manifest to update).

**Data flow:** Page load → fetch each `moods/<année>.json` from `START_YEAR` to the current year → merge → display today's mood + stats → render 3-month calendar with color-coded days.

**Timeline stats** (`displayTimeline()`): sous le graphique d'évolution, deux cartes « rouge » / « vert » donnent le nombre de jours et la plus longue période. Rouge/vert se définissent comme sur le graphique : la moyenne mobile sur 7 jours (la courbe de tendance) sous / au-dessus du niveau 3, et non la note brute du jour. Le survol d'une carte met la période record en évidence sur le graphique.

**Baromètre** (`displayForecast()`): cadran SVG qui indique la tendance des 3 prochains jours (aujourd'hui, ou demain si déjà saisi). Prévision par jour = moyenne historique du jour de la semaine + écart de forme des 7 derniers jours, qui s'estompe de 40 % par jour de distance (fonctionne même si des jours sont oubliés). L'aiguille montre l'écart entre cette prévision et la forme récente (±1 point = ±75°, seuil « stable » à ±0,25). C'est volontairement une métaphore : sur ces données, aucun modèle ne bat vraiment la moyenne (erreur ~0,95 point), on lit une direction, pas une valeur. Masqué sous 28 jours de données.

**Ikigai** (`displayIkigai()`): carte « 🌸 Mon Ikigai » sous le calendrier, sans notion de temps : juste le ressenti du moment. Lue depuis `ikigai.json` à la racine, quatre booléens `aime`, `doue`, `besoin`, `paye` (une réponse oui / non par cercle). Chaque combinaison correspond à une zone de `IKIGAI_ZONES` (Passion, Mission, Vocation, Profession, Ikigai…), affichée sur un diagramme de Venn SVG : cercles à oui colorés, cercles à non en pointillé, un point sur la zone. Carte masquée si le fichier est absent. Les 16 cas possibles ne sont montrés que dans la modale « ❓ Aide », ouverte par le lien « Comment lire ce schéma ? » de la carte (`displayHelp()`, `openHelp()`).

**Mood scale:** 1 (😢 Difficile) → 5 (😄 Excellent).

## Content Language

The app UI and README are written in **French**.

## Git Commit Convention

Mood entry commits follow the pattern: `🎭 Humeur du JJ/MM : [emoji]`

Ikigai commits list the emoji of the circles answered yes: `🌸 Ikigai : ❤️💪💰` (`—` when none).
