# Comment fonctionne le workflow WakaTime dans ce repo?

## Vue d'ensemble

Ce dépôt utilise un workflow GitHub Actions pour automatiquement mettre à jour les statistiques de codage WakaTime dans le fichier README.md. Le workflow s'exécute automatiquement toutes les 2 heures et peut également être déclenché manuellement.

## Configuration du Workflow

### Fichier de Workflow

Le workflow est défini dans `.github/workflows/Code Stats.yml` :

```yaml
name: Waka Readme

on:
  workflow_dispatch:
  schedule:
    # Runs every 2 hours
    - cron: "0 */2 * * *"

jobs:
  update-readme:
    name: Update this repo's README
    runs-on: ubuntu-latest
    steps:
      - uses: athul/waka-readme@v0.3.0
        with:
          WAKATIME_API_KEY: ${{ secrets.WAKATIME_API_KEY }}
```

### Composants du Workflow

#### 1. **Déclencheurs (Triggers)**

Le workflow peut être déclenché de deux façons :

- **`workflow_dispatch`** : Permet un déclenchement manuel depuis l'onglet Actions de GitHub
- **`schedule` avec cron** : S'exécute automatiquement toutes les 2 heures (`"0 */2 * * *"`)

#### 2. **Job : update-readme**

Le workflow contient un seul job qui s'exécute sur `ubuntu-latest` et utilise l'action GitHub `athul/waka-readme@v0.3.0`.

#### 3. **Action utilisée : athul/waka-readme**

Cette action :
- Se connecte à l'API WakaTime en utilisant votre clé API
- Récupère vos statistiques de codage de la semaine dernière
- Met à jour automatiquement la section du README.md délimitée par les marqueurs spéciaux
- Commit les changements dans le dépôt

#### 4. **Secret requis : WAKATIME_API_KEY**

Le workflow nécessite que la clé API WakaTime soit configurée comme secret GitHub :
- **Nom du secret** : `WAKATIME_API_KEY`
- **Valeur** : Votre clé API WakaTime (obtenue depuis votre compte WakaTime)

## Fonctionnement dans le README

### Section mise à jour automatiquement

Dans le fichier `README.md`, les statistiques WakaTime sont affichées entre les marqueurs suivants :

```markdown
## ⏱️ Coding Stats

<!--START_SECTION:waka-->

```txt
C#                                 12 hrs 41 mins  ████████░░░░░░░░░░░░░░░░░   31.39 %
VB.NET                             11 hrs 15 mins  ███████░░░░░░░░░░░░░░░░░░   27.84 %
XML                                4 hrs 46 mins   ███░░░░░░░░░░░░░░░░░░░░░░   11.79 %
YAML                               3 hrs 46 mins   ██▒░░░░░░░░░░░░░░░░░░░░░░   09.32 %
Binary                             3 hrs 43 mins   ██▒░░░░░░░░░░░░░░░░░░░░░░   09.19 %
```

<!--END_SECTION:waka-->
```

**Important** : Ne modifiez jamais manuellement le contenu entre `<!--START_SECTION:waka-->` et `<!--END_SECTION:waka-->`. Le workflow écrasera toute modification manuelle.

## Configuration de WakaTime

### Prérequis

1. **Compte WakaTime** : Créez un compte gratuit sur [wakatime.com](https://wakatime.com/)
2. **Plugin WakaTime** : Installez le plugin WakaTime dans votre IDE (VS Code, Visual Studio, IntelliJ, etc.)
3. **Clé API** : Récupérez votre clé API depuis les paramètres de votre compte WakaTime

### Configuration du Secret GitHub

Pour que le workflow fonctionne, vous devez configurer le secret `WAKATIME_API_KEY` :

1. Allez dans **Settings** > **Secrets and variables** > **Actions**
2. Cliquez sur **New repository secret**
3. Nom : `WAKATIME_API_KEY`
4. Valeur : Collez votre clé API WakaTime
5. Cliquez sur **Add secret**

## Utilisation

### Déclenchement Automatique

Le workflow s'exécute automatiquement toutes les 2 heures. Vous n'avez rien à faire, les statistiques seront mises à jour automatiquement.

### Déclenchement Manuel

Pour mettre à jour manuellement les statistiques :

1. Allez dans l'onglet **Actions** du dépôt
2. Sélectionnez le workflow **Waka Readme**
3. Cliquez sur **Run workflow**
4. Sélectionnez la branche (généralement `main`)
5. Cliquez sur **Run workflow**

## Vérification du Statut

### Voir l'historique des exécutions

1. Allez dans l'onglet **Actions**
2. Sélectionnez **Waka Readme** dans la liste des workflows
3. Vous verrez l'historique de toutes les exécutions avec leur statut (succès ou échec)

### Badge de Statut

Vous pouvez ajouter un badge de statut du workflow dans votre README :

```markdown
![Waka Readme](https://github.com/vic-odes/vic-odes/workflows/Waka%20Readme/badge.svg)
```

## Dépannage

### Le workflow échoue

**Causes possibles** :

1. **Clé API invalide ou expirée**
   - Solution : Vérifiez que le secret `WAKATIME_API_KEY` est correctement configuré
   - Générez une nouvelle clé API si nécessaire

2. **Marqueurs manquants dans le README**
   - Solution : Assurez-vous que les marqueurs `<!--START_SECTION:waka-->` et `<!--END_SECTION:waka-->` sont présents dans le README.md

3. **Permissions insuffisantes**
   - Solution : Vérifiez que le workflow a les permissions d'écriture sur le dépôt
   - Allez dans **Settings** > **Actions** > **General** > **Workflow permissions** et activez "Read and write permissions"

### Les statistiques ne se mettent pas à jour

1. Vérifiez que WakaTime est actif et enregistre votre activité de codage
2. Vérifiez que vous avez codé récemment (WakaTime affiche les statistiques de la dernière semaine)
3. Consultez les logs du workflow pour voir s'il y a des erreurs

### Personnalisation

L'action `athul/waka-readme` supporte plusieurs options de personnalisation. Vous pouvez les ajouter dans la section `with` du workflow :

```yaml
- uses: athul/waka-readme@v0.3.0
  with:
    WAKATIME_API_KEY: ${{ secrets.WAKATIME_API_KEY }}
    SHOW_TITLE: true                    # Afficher un titre
    BLOCKS: ->                          # Personnaliser les barres de progression
    TIME_RANGE: last_7_days            # Période des statistiques
    SHOW_TIME: true                     # Afficher le temps de codage
    SHOW_TOTAL: true                    # Afficher le temps total
```

## Ressources

- [Documentation de l'action waka-readme](https://github.com/athul/waka-readme)
- [Documentation WakaTime](https://wakatime.com/help)
- [Documentation GitHub Actions](https://docs.github.com/en/actions)
- [Syntaxe Cron](https://crontab.guru/)

## Badge WakaTime

Le README inclut également un badge WakaTime qui affiche le temps total de codage :

```markdown
<img src="https://wakatime.com/badge/user/b2e9dd80-f633-4e17-abca-e54af8c6a554.svg" alt="vic-odes" />
```

Ce badge est mis à jour en temps réel par WakaTime et ne nécessite pas le workflow GitHub Actions.
