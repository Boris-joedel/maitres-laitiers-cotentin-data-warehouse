# Maîtres Laitiers du Cotentin — Data Warehouse & BI

## Contexte du projet

Je voulais un projet de portfolio qui aille au-delà d'un simple dataset Kaggle reformaté : je suis parti d'un jeu de données générique (ventes/inventaire laitier indien) et je l'ai entièrement repensé pour représenter une **coopérative laitière normande réelle**, Les Maîtres Laitiers du Cotentin — ses vraies marques (Valco, Campagne de France, Réo, Montebourg, Val de Saire, Yéo...), ses vrais sites de production (Sottevast, Valognes, Méautis, Lessay...), et sa vraie structure d'activité de coopérative : collecte de lait auprès d'exploitations agricoles, transformation, distribution vers la grande distribution et la restauration hors foyer.

L'objectif : construire une chaîne complète data warehouse → BI, comme celle qu'on retrouve dans une offre d'emploi data analyst/data engineer confirmé, avec un vrai modèle de données métier plutôt qu'un dataset générique.

## Pourquoi un modèle en constellation plutôt qu'une étoile simple

Une coopérative laitière a plusieurs métiers distincts (collecte, production, stockage, vente, logistique) qui partagent des référentiels communs (dates, sites, produits) mais génèrent chacun leurs propres événements. Un schéma en étoile classique (une seule table de faits) aurait forcé à tout entasser dans une seule table ou à perdre la richesse de chaque métier. J'ai donc opté pour un **modèle en constellation** : 5 tables de dimensions partagées par 5 tables de faits distinctes, chacune représentant un processus métier réel :

| Table de faits | Processus métier représenté |
|---|---|
| `FACT_VENTES` | Transactions commerciales vers les clients (GMS/RHF) |
| `FACT_LIVRAISONS` | Suivi logistique des livraisons (délais, statuts) |
| `FAIT_PRODUCTION` | Lots de production par site et par produit |
| `FACT_COLLECTE_LAIT` | Collecte de lait auprès des exploitations agricoles (spécificité coopérative) |
| `FACT_STOCK` | Suivi des niveaux de stock par site et produit |

*(Note : j'ai gardé volontairement le préfixe `FACT_` en anglais pour la plupart des tables de faits et `FAIT_` pour une seule — un choix de nomenclature assumé plutôt qu'une incohérence.)*

## Architecture technique

```
Génération des données (Python / pandas / numpy)
        ↓
Snowflake (data warehouse cloud) — 10 tables, ~1,26M lignes, 2019-2024
        ↓
Power BI (import) — modèle relationnel + mesures DAX
        ↓
Talend — pipeline d'alimentation incrémentale journalière (en cours)
```

## Ce que le modèle couvre

- **6 ans d'historique** (2019-2024), avec saisonnalité et volumes réalistes
- **142 clients** (grande distribution + restauration hors foyer) répartis dans **30 villes françaises**, avec latitude/longitude pour la cartographie
- **120 exploitations agricoles** normandes (fournisseurs de lait), avec certification bio/conventionnel
- **20 produits réels** des marques du groupe, avec prix, conditionnement, durée de conservation
- **7 sites** (production, affinage, distribution)

## Choix méthodologiques et limites assumées

- Les produits, marques et sites sont **réels et documentés publiquement** (Wikipédia, Wikimanche, sites officiels du groupe et de son réseau de distribution France Frais)
- Les volumes de vente, prix, clients et fournisseurs sont **générés** de façon réaliste (ordres de grandeur cohérents avec un groupe coopératif laitier régional) mais ne reflètent **pas** les chiffres réels de l'entreprise — ce projet est un exercice de portfolio, pas une reproduction de données confidentielles
- La génération de données et une partie de la structuration ont été réalisées avec l'aide d'un assistant IA (Claude, Anthropic) comme accélérateur — au même titre que j'utilise Talend, Snowflake ou Power BI comme outils. Les choix de modélisation, la compréhension du modèle et son exploitation en BI sont les miens.

## Stack technique

- **Python** (pandas, numpy) — génération et structuration des données
- **Snowflake** — data warehouse cloud
- **Power BI** — modélisation relationnelle, mesures DAX, dashboards
- **Talend** — ETL / pipeline d'ingestion incrémentale

## Roadmap

- [x] Modélisation en constellation (5 dimensions + 5 faits)
- [x] Génération des données historiques 2019-2024
- [x] Chargement dans Snowflake
- [ ] Modèle relationnel et mesures DAX dans Power BI
- [ ] Dashboards (ventes, logistique, collecte de lait, stocks)
- [ ] Pipeline Talend d'alimentation incrémentale journalière (append automatisé)

## Structure du repo

```
sql/        → scripts DDL Snowflake
python/     → scripts de génération des données (historique + incréments journaliers)
docs/       → guide de mise en œuvre détaillé
screenshots/→ captures des dashboards Power BI (à venir)
```

---

*Projet réalisé par Boris-Joël dans le cadre de la construction de son portfolio data analyst / data engineer.*
