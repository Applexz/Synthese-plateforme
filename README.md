# Synthèse des avis par plateforme

Une page web avec un bouton : elle lit les réponses d'un Google Sheet (alimenté par un Google Forms), les regroupe par plateforme et demande à Claude une synthèse par plateforme et par critère.

```
GitHub Pages (index.html)  --clic-->  Apps Script (Code.gs, lié au Sheet)  -->  API Claude
        affichage des synthèses  <--------------- JSON ------------------------
```

La clé API Anthropic reste dans les propriétés de l'Apps Script (côté Google). Elle n'est jamais dans ce dépôt ni dans la page.

## Mise en place (environ 10 minutes)

### 1. Ajouter le moteur dans le Sheet
1. Ouvrez le Google Sheet des réponses.
2. Menu **Extensions > Apps Script**.
3. Remplacez le contenu de `Code.gs` par celui de [`apps-script/Code.gs`](apps-script/Code.gs).
4. Enregistrez.

La colonne A (Horodateur) est ignorée, la colonne B doit contenir le nom de la plateforme et les colonnes suivantes les réponses aux critères. Les noms de plateforme sont regroupés sans tenir compte des majuscules, des accents ni des espaces en trop.

### 2. Renseigner la clé et le code d'accès
Dans l'éditeur Apps Script : **Paramètres du projet (roue dentée) > Propriétés du script > Ajouter une propriété**.

| Propriété | Valeur |
|---|---|
| `ANTHROPIC_API_KEY` | votre clé API Anthropic |
| `ACCESS_CODE` | un code de votre choix, à saisir sur la page |
| `SHEET_NAME` | (optionnel) nom de l'onglet des réponses, sinon le premier onglet |
| `MODEL` | (optionnel) modèle Claude, par défaut `claude-sonnet-5-5` |

### 3. Déployer en application web
1. **Déployer > Nouveau déploiement > Application web**.
2. *Exécuter en tant que* : **Moi**.
3. *Qui a accès* : **Tout le monde** (le code d'accès protège l'usage).
4. Autorisez les accès demandés (lecture du Sheet, appels externes).
5. Copiez l'adresse qui se termine par `/exec`.

À chaque modification de `Code.gs`, refaites **Déployer > Gérer les déploiements > Modifier > Nouvelle version**, sinon l'ancienne version reste en ligne.

### 4. Publier la page
1. Dans ce dépôt GitHub : **Settings > Pages > Source : Deploy from a branch**, branche `main`, dossier `/ (root)`.
2. Ouvrez l'adresse GitHub Pages indiquée (du type `https://applexz.github.io/Synthese-plateforme/`).
3. Collez l'adresse `/exec` et le code d'accès, puis cliquez sur **Actualiser les synthèses**.

Pour ne plus avoir à coller l'adresse, renseignez `DEFAULT_APPS_SCRIPT_URL` en haut du script de `index.html`.

## Utilisation
- **Actualiser les synthèses** : relit le Sheet. Si les réponses n'ont pas changé depuis la dernière analyse (moins de 6 h), le résultat conservé est renvoyé sans nouvel appel à l'API.
- **Forcer une nouvelle analyse** : relance l'analyse même sans nouvelle réponse.
- **Voir un exemple** : affiche des données fictives, sans rien appeler.
- **Imprimer / PDF** : déplie tous les critères et ouvre l'impression.

## Test sans la page
Dans l'éditeur Apps Script, choisissez la fonction `testerSynthese` et lancez-la : le résultat s'affiche dans les journaux.

## Bon à savoir
- Les réponses (texte libre) sont envoyées à l'API Anthropic pour être synthétisées.
- Le code d'accès limite l'usage de votre clé, mais l'adresse `/exec` reste publique : ne la diffusez pas largement, et changez `ACCESS_CODE` après l'atelier si besoin.
- Avec peu de répondants par plateforme, les synthèses sont indicatives, et le prompt demande au modèle de le signaler.
