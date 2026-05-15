# Rétroaction automatisée -- S01 (Diagnostic fondamental -- NexaMart kickoff)

_Générée le 2026-05-15T01:36:39+00:00 -- Run `20260515T013554Z-4cff8f73`_

Ce document est produit par un pipeline reproductible (vérification SQL déterministe + analyse LLM du brief et de la déclaration IA). Une revue humaine précède toujours sa publication. **À ce stade expérimental, aucune note ni étiquette de niveau n'est diffusée : l'objectif est purement formatif.**

---

## 1. Vérification automatique de la requête SQL

La requête extraite de votre brief n'a pas pu être validée automatiquement. Quelques pistes constructives ci-dessous pour vous aider à la rendre exécutable et alignee avec la question posée.

_Observation technique : colonnes manquantes (oracle): revenue_

<details><summary>Requête analysée — cliquez pour déplier</summary>

```sql
WITH sales_by_period AS (
    SELECT
        p.category,
        s.region,
        f.channel_id AS channel,
        d.year,
        d.quarter,
        SUM(f.line_total) AS revenue
    FROM raw_fact_sales f
    JOIN raw_dim_product p ON f.product_id = p.product_id
    JOIN raw_dim_store s   ON f.store_id   = s.store_id
    JOIN raw_dim_date d    ON f.order_date = d.date_key
    GROUP BY
        p.category,
        s.region,
        f.channel_id,
        d.year,
        d.quarter
),

sales_decline AS (
    SELECT
        category,
        region,
        channel,
        year,
        quarter,
        revenue,
        LAG(revenue) OVER (
            PARTITION BY category, region, channel
            ORDER BY year, quarter
        ) AS revenue_prev_period
    FROM sales_by_period
),

regional_worst AS (
    SELECT
        category,
        region,
        channel,
        year,
        quarter,
        revenue - revenue_prev_period AS decline_amount,
        ROW_NUMBER() OVER (
            PARTITION BY region
            ORDER BY revenue - revenue_prev_period ASC
        ) AS rn
    FROM sales_decline
    WHERE revenue_prev_period IS NOT NULL
      AND revenue < revenue_prev_period
      AND year = 2025
)
SELECT
    region,
    category,
    channel,
    year,
    quarter,
    decline_amount
FROM regional_worst
WHERE rn = 1
ORDER BY region;
```

</details>

- Colonnes retournées : `region, category, channel, year, quarter, decline_amount`
- Correspondance avec les colonnes attendues :
  - `category` → `category`
  - `region` → `region`
  - `quarter` → `quarter`
  - `revenue` → `(à ajouter ou renommer)`

**Pistes :**
> Requête extraite par LLM (aucun bloc fencé détecté). Encadrez votre requête finale par ```sql ... ``` pour éliminer toute ambiguïté.
> Votre `db/nexamart.duckdb` est absente ou vide ; la requête a été exécutée contre une **base de référence cohorte** (seed instructeur). Les chiffres retournés ne correspondent donc pas à vos propres données : reconstruisez votre base avec `python src/run_pipeline.py` (ou `.\run.ps1 load`) pour valider vos calculs sur votre seed personnel.
> Synonymes acceptés par colonne:
  category: ['category', 'categorie', 'p.category', 'sous_categorie']
  region: ['region', 's.region']
  quarter: ['quarter', 'trimestre', 'd.quarter', 'q']
  revenue: ['net_revenue', 'revenue', 'revenu', 'total_revenue', 'ca', 'sales', 'line_total', 'gross_revenue']

## 2. Rétroaction pédagogique sur le brief

> Le brief produit des résultats exploitables pour 2025 et une requête de validation correcte, mais il manque la déclaration explicite du grain, la gestion des cas limites et une traçabilité git complète. Pour rendre le livrable board-ready, formalisez le grain et les patterns SCD, renforcez les checks automatisés et ajoutez un journal de décisions et commits.

### Observations par dimension

**Model quality**
- Observation : Le brief agrège par category, region et channel (GROUP BY p.category, s.region, f.channel_id, d.year, d.quarter) mais n'énonce pas explicitement le grain ni les choix SCD.
- Piste d'amélioration : Déclarer le grain (ligne de commande vs commande), justifier le pattern SCD (Type 2 si historique nécessaire) et adapter le schéma en étoile (fact + 5 dims).

**Validation quality**
- Observation : Une requête de validation avec LAG() est fournie et le résultat tabulaire pour 2025 est affiché, prouvant que la requête s'exécute et produit des déclins par région.
- Piste d'amélioration : Ajouter des contrôles make check (NULLs, doublons du grain, vérification des sommes vs lignes sources) et traiter explicitement les cas limites (NULL, valeurs aberrantes, unit_price non-additif).

**Executive justification**
- Observation : La section 'Réponse exécutive' liste clairement quelles catégories/channels régionales déclinent (ex. Automotive en Alberta Q4 2025) et propose d'intégrer 2023–2024 pour creuser les causes.
- Piste d'amélioration : Formuler en début de section une recommandation décisionnelle claire pour le CEO (p.ex. valider l'enrichissement des années 2023–24 et autoriser la construction de l'entrepôt SCD v1).

**Process trace**
- Observation : Le brief inclut une commande duckdb et un chemin de workspace (@ches1702 ➜ /workspaces/...), mais il n'y a pas d'historique git ni de note IA structurée.
- Piste d'amélioration : Inclure un log de commits git incrémental (≥3 commits) avec messages, et une note IA précisant outil, prompt et validation humaine.

**Reproducibility**
- Observation : La commande DuckDB fournie montre comment exécuter la requête, mais le chemin est codé en dur et il manque un README/make check reproduisible pour un clone propre.
- Piste d'amélioration : Fournir un README et un script 'make check' reproduisible, éviter les chemins codés en dur et décrire les dépendances pour exécution sur un clone propre.

## 3. Déclaration d'utilisation de l'IA

> La déclaration documente bien les séances, les prompts et les actions humaines de validation. Il manque toutefois des précisions importantes (version/modèle des outils et mention explicite des limites ou erreurs observées).

**Sujets bien couverts dans votre déclaration :**

- à quelle étape l'IA a été utilisée
- comment la sortie a été validée par l'humain

**Sujets à ajouter ou expliciter pour la prochaine itération :**

- outils utilisés (nom + version/modèle)
- limites ou erreurs observées

## 4. Pistes d'action pour la prochaine itération

- Reprendre la requête de la section « Preuve » pour qu'elle s'exécute sur `db/nexamart.duckdb` et qu'elle produise la forme attendue (voir pistes en section 1).
- Compléter `ai-usage.md` en y ajoutant : outils utilisés (nom + version/modèle).
- Compléter `ai-usage.md` en y ajoutant : limites ou erreurs observées.

---

## 5. Traçabilité

- **Run ID :** `20260515T013554Z-4cff8f73`
- **Devoir :** `S01`
- **Étudiant·e :** `ches1702`
- **Commit analysé :** `64b6017`
- **Audit (côté instructeur) :** `tools/instructor/feedback_pipeline/audit/20260515T013554Z-4cff8f73/ches1702/`
- **Prompts (SHA-256) :**
  - `sql_extractor_system` : `90ee9e277de7a27f...`
  - `rubric_grader_system` : `505f32d1d8319d66...`
  - `ai_usage_grader_system` : `81cb7fdf89bda55a...`
- **Fournisseur (rubrique) :** `openai`
- **Fournisseur (IA-usage) :** `openai` (gpt-5-mini-2025-08-07)

_Ce feedback a été produit par un pipeline automatisé et **revu par l'équipe pédagogique avant publication**. Aucun chiffre ni étiquette de niveau n'est diffusé à ce stade expérimental : l'objectif est uniquement formatif. Ouvrez une issue dans ce dépôt pour toute question._
