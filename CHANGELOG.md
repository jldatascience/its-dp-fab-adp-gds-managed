# Changelog

Toutes les évolutions notables du dépôt `its-dp-fab-adp-gds-managed` (configuration de déploiement,
pipelines, outillage, documentation) sont consignées ici, de la plus récente à la plus ancienne.

Format : [Keep a Changelog](https://keepachangelog.com/fr/1.1.0/). Ce dépôt n'est pas versionné
par tag : chaque entrée est datée (`## AAAA-MM-JJ`). Les évolutions du socle commun sont décrites
dans le `CHANGELOG.md` de `its-dp-fab-cicd-common`, versionné par tag `vX.Y.Z`.

## 2026-11-01

### Changed

- Fichiers issus d'une copie du dépôt Framework (commit `38a4853`), réécrits pour l'ADP
  GDS (socle `its-dp-fab-cicd-common`, tag `v1.5.0`, catégorie `adp` ;
  références : dépôt ADP MDS pour la dépendance Framework, dépôts UCS, BDP (Performance Engagement,
  Facture Client, Engagement, Compte, Collaborateur, Centre Profit) et ADP IBS pour la forme et
  l'outillage) :
  - `CICD/pipelines/CI.yml` : template KPMG conservé (le socle n'a pas de template CI pour les
    consommateurs) ; SonarQube repris de l'ancienne CI ADP ; déclencheurs `develop`, `release/*`,
    `main` ; filtre d'artefact `"!/*.json"` (l'ancien `!**/*.json` non quoté est écarté).
  - `CICD/pipelines/CD.yml` : `repository: its-dp-fab-adp-gds-managed`,
    `role: consumer`, source CI `its-dp-fab-gds-managed-CI`,
    `frameworkGate: ado` (Framework seul prérequis, R1), `frameworkPipelineName` en marqueur,
    coffre `frfrkvdevdp0001`, 3 service connections ARM distinctes ITG, PPR, PRD (décision du 31/10/2026).
  - `CICD/pipelines/PR-Validation.yml` : en-tête ADP, contenu inchangé.
  - `CICD/deployment/config.yml` : workspaces `FR - DP - <ENV> - FAB - ADP - GDS - MANAGED`
    pour DEV, ITG, PPR et PRD (confirmés le 01/11/2026, tiret simple) ; `item_types_in_scope` vide ;
    clé `core.parameter`, `folder_exclude_regex` et `features` retirés.
  - `CICD/deployment/dependencies.yml` : `role: consumer`, `category: adp`, `framework.required: true`
    (workspaces Framework, `items: []`, `ado_gate`) ; connexion `DP-Fabric-Global-APR`, Variable
    Library `vl_core_config` et `guid_policy` du Framework retirés ; `guid_policy.forbidden_prefixes`
    limité au préfixe du workspace GDS DEV (`69943001-`, relevé dans l'ancien deploy.py).
  - `CICD/deployment/parameter.yml` : 56 règles Framework retirées ; aucune règle ; en-tête décrivant
    les deux familles admises (workspace courant, workspace Framework par environnement).
  - `CICD/ref_dynamique/01_inventaire_github.py` à `04_recoupement.py` : repris de l'outillage UCS
    (dépôt seul, une branche, sorties hors du dépôt, activités imbriquées) et étendus au Framework :
    `02` relève aussi le workspace Framework du même environnement ; `03` propose les règles
    Framework par environnement et `framework_items_adp.yml` ; `04` traite les références Framework
    comme légitimes et signale `FRAMEWORK_NON_DECLARE`.
  - `CICD/ref_dynamique/templates/config.yml` et `parameter.yml` : modèles ADP au format du socle
    (noms de workspaces, aucun GUID, aucune clé en minuscules).
  - `.gitignore` : remplacé par celui des dépôts MDS, Alexandrie et UCS, à l'identique
    (`.gitattributes` était déjà identique).
  - README réécrit pour l'ADP ; ce journal remplace celui du Framework, copié par erreur.

### Fixed

- Noms des workspaces GDS : suffixe `MANAGED` en majuscules pour DEV, ITG, PPR et PRD (au lieu de
  `Managed`), confirmé dans le portail Fabric le 01/11/2026 ; le pré-vol compare les noms caractère
  par caractère.

### Removed

- `fabric/test.md` (contenu « test », référencé par aucun pipeline ni script ; suppression validée).

### Added

- `fabric/.gitkeep` : dossier des items suivi par Git avant la première synchronisation.

### Vérifié

- `yamllint` (règles du socle) sur `CICD/deployment` et `CICD/pipelines` : sans erreur.
- `python -m compileall CICD/ref_dynamique` : sans erreur ; syntaxe compatible Python 3.9.
- `deploy.py validate` (socle `v1.5.0`) : code 4 attendu (« Aucun item Fabric »). Sur une copie de
  travail munie d'un item fictif : 0 erreur, `repository_directory` résolu vers `fabric/`, matrice
  `adp` acceptée.
- Scripts 01 à 04 exécutés sur le dépôt (vide) et sur un jeu d'essai hors dépôt (appel d'un
  notebook ADP, d'un pipeline Framework et d'un workspace tiers) : règles des deux familles
  acceptées par `deploy.py validate`, référence tierce écartée, aucune écriture dans le dépôt.

### À confirmer

- Organisation et service connection GitHub ; noms des 3 service connections ARM ITG, PPR et PRD (création prévue).
- Casse exacte du pipeline CI dans Azure DevOps (`its-dp-fab-gds-managed-CI`, sans « adp », repris de
  l'ancienne CD) ; nom exact de la CD Framework.
- Types d'items, règles, `framework.items`, connexions Fabric, Variable Libraries, `guid_policy` et
  `data_dependencies`, après synchronisation Git Integration.
- Point V-01 : le filtre `contents: "!/*.json"` de l'artefact CI ne doit pas exclure les fichiers d'items.
