# Mon Niko Niko 📊

Application web simple pour afficher votre humeur du jour, inspirée du calendrier Niko Niko utilisé dans les équipes agiles.

## Qu'est-ce qu'un Niko Niko ?

Le Niko Niko (ニコニコ signifiant "sourire" en japonais) est un outil de visualisation d'humeur permettant de suivre votre état émotionnel au fil du temps.

## Fonctionnement

Cette application affiche votre humeur du jour en lisant les fichiers `moods/<année>.json` (un par année, chargés automatiquement de 2026 jusqu'à l'année en cours).

### Affichage

- 🎭 Grande visualisation de l'humeur du jour avec l'emoji d'humeur et de raison (et son interprétation)
- 📊 Statistiques simples (nombre de jours, moyenne)
- 📈 Évolution de l'humeur : graphique jour par jour avec courbe de tendance (moyenne mobile sur 7 jours), week-ends et vacances distingués
- 🔴🟢 Jours dans le rouge / le vert et leur plus longue période : la tendance passe sous / au-dessus du niveau 3. Survolez une carte pour voir la période sur le graphique
- 🧭 Baromètre de l'humeur : un cadran dont l'aiguille indique la tendance des 3 prochains jours (voir plus bas)
- 📅 Calendrier des derniers mois pour voir les tendances en un coup d'œil
- 🎨 Design épuré et animé

### Baromètre de l'humeur

Une prévision sur le modèle de la météo : imprécise, mais qui s'appuie sur ce qui s'est déjà produit. Pour chacun des 3 prochains jours (à partir d'aujourd'hui, ou de demain si l'humeur du jour est déjà saisie), on prend la moyenne historique de ce jour de la semaine, corrigée de l'écart de forme des 7 derniers jours. Cet écart s'estompe de 40 % par jour de distance, donc oublier de saisir quelques jours ne bloque pas la prévision.

L'aiguille compare cette prévision à votre forme récente : à gauche une baisse, au centre stable, à droite une amélioration (seuil « stable » à ±0,25 point). On lit une direction, pas une valeur précise. Le baromètre n'apparaît qu'à partir de 28 jours de données.

### Mise à jour des humeurs

Les humeurs sont enregistrées dans `moods/<année>.json` (par exemple `moods/2026.json`). Pour ajouter ou modifier une humeur, éditez simplement ce fichier et commitez les changements via Git. Pour une nouvelle année, créez `moods/<année>.json` avec `{"moods": {}}` : la page le charge automatiquement.

## Format des fichiers moods/<année>.json

```json
{
  "moods": {
    "2026-01-17": {
      "value": "1",
      "reason": "💻"
    },
    "2026-01-18": {
      "value": "2",
      "reason": "🏠"
    }
  }
}
```

### Structure des entrées

Chaque entrée contient :
- **`value`** (1-5) : Le niveau d'humeur (l'emoji d'humeur est automatiquement mappé depuis cette valeur)
- **`reason`** (optionnel) : Un emoji représentant la raison/contexte de cette humeur (ex: 💻 travail, 🏠 maison, 👨‍👩‍👧‍👦 famille, etc.). Un emoji de raison sans interprétation dans `EMOJI_INTERPRETATIONS` (`index.html`) s'affiche sans explication
- **`vacation`** (optionnel, booléen) : `true` pour marquer un jour de vacances (affiché en vert dans le graphique)

Une entrée par ligne (JSON compact) rend le fichier facile à parcourir à la main.

### Les 5 niveaux d'humeur

- **5** : 😄 Excellent - Une excellente journée !
- **4** : 🙂 Bien - Bonne humeur générale
- **3** : 😐 Neutre - Journée normale
- **2** : 😕 Moyen - Quelques difficultés
- **1** : 😢 Difficile - Journée difficile

### Exemples d'emojis de raison

- 💻 Travail
- 🏠 Maison
- 👨‍👩‍👧‍👦 Famille
- 🏃 Sport/Santé
- 💰 Finances
- ✈️ Voyage
- 🎉 Événement spécial
- 😴 Fatigue
- etc.

## Visualisation du calendrier

Le calendrier affiche les derniers mois avec :
- Les jours du mois dans une grille (format semaine : Lun-Dim)
- L'emoji d'humeur pour chaque jour enregistré
- L'emoji de raison en dessous (si renseigné)
- Le jour actuel mis en évidence
- Les jours avec humeur ont un fond coloré et s'agrandissent au survol

Cette vue permet de visualiser rapidement vos tendances d'humeur et d'identifier les patterns sur les dernières semaines.

## Workflow Git

Pour mettre à jour votre humeur du jour :

1. Éditez le fichier `moods/<année>.json`
2. Ajoutez ou modifiez l'entrée pour la date du jour (format: `AAAA-MM-JJ`)
3. Commitez le changement : `git commit -m "🎭 Humeur du JJ/MM : [emoji]"`
4. Pushez vers GitHub : `git push`
5. Rafraîchissez la page web pour voir la mise à jour

## Exemple de commit

```bash
# Éditer moods/2026.json pour ajouter l'humeur du jour
# Exemple : value: 2 (Moyen 😕), reason: 🏠 (maison)
git add moods/2026.json
git commit -m "🎭 Humeur du 18/01 : 😕 (🏠)"
git push
```

## Technologie

Application web simple utilisant :
- **HTML5** : Structure de la page
- **CSS3** : Design moderne avec animations
- **JavaScript vanilla** : Chargement, statistiques, graphique et baromètre (SVG)
- **Fetch API** : Lecture des fichiers moods/<année>.json

Pas de backend, pas de configuration compliquée - juste un fichier JSON et du HTML/CSS/JS pur !

---

Prenez soin de vous et suivez votre bien-être au quotidien ! 🌟
