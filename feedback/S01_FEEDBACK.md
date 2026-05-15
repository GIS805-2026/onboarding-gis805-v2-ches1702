# Rétroaction automatisée -- S01 (Diagnostic fondamental -- NexaMart kickoff)

_Générée le 2026-05-15T12:37:42+00:00 -- Run `20260515T122624Z-00a5a04f`_

Ce document est produit par un pipeline reproductible (vérification SQL déterministe + analyse LLM du brief et de la déclaration IA). Une revue humaine précède toujours sa publication. **À ce stade expérimental, aucune note ni étiquette de niveau n'est diffusée : l'objectif est purement formatif.**

> ⚠️ **Avertissement instructeur (à retirer avant publication) :** cette analyse a été générée avec `--skip-pull`. Le contenu correspond au commit local et **n'est peut-être pas la dernière version poussée par l'étudiant·e**.

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
> Synonymes acceptés par colonne:
  category: ['category', 'categorie', 'p.category', 'sous_categorie']
  region: ['region', 's.region']
  quarter: ['quarter', 'trimestre', 'd.quarter', 'q']
  revenue: ['net_revenue', 'revenue', 'revenu', 'total_revenue', 'ca', 'sales', 'line_total', 'gross_revenue']

## 2. Rétroaction pédagogique sur le brief

> Le livrable répond partiellement à la question CEO et produit des résultats exploitables pour 2025, mais il manque une énonciation claire du grain, des contrôles qualité reproductibles et une traçabilité git/IA complète. Priorisez la formalisation du grain, l'ajout de checks make check et un README exécutable pour rendre l'analyse production-ready.

### Observations par dimension

**Model quality**
- Observation : Le brief inclut la requête GROUP BY sur p.category, s.region, f.channel_id, d.year, d.quarter mais n'énonce pas clairement le grain ni ne traite les SCD ou la non-additivité du unit_price.
- Piste d'amélioration : Précisez explicitement le grain (par ex. ligne de commande order_id+product_id), justifiez le pattern SCD choisi et mentionnez les attributs dimensionnels clés et pourquoi.

**Validation quality**
- Observation : L'auteur fournit une requête avec fenêtre LAG et un tableau de résultats montrant les declines par région et trimestre pour 2025.
- Piste d'amélioration : Ajoutez des contrôles de qualité reproductibles (make check) et traitez les cas limites (NULLs, doublons du grain, vérification que SUM(line_total) correspond aux attentes).

**Executive justification**
- Observation : La section 'Réponse exécutive' liste les catégories/régions/trimestres en baisse et la recommandation d'intégrer 2023–2024 pour investiguer les causes.
- Piste d'amélioration : Formulez une recommandation décisionnelle claire et priorisée (ex. action à lancer cette semaine, impact attendu chiffré) et réduisez le détail technique pour le conseil d'administration.

**Process trace**
- Observation : Le brief montre une commande DuckDB depuis /workspaces/... mais il n'y a pas d'historique de commits ni une note IA détaillée sur l'usage de Claude.
- Piste d'amélioration : Incluez un historique git avec plusieurs commits significatifs et une note IA précisant l'outil, ce qu'il a généré, et comment vous l'avez validé manuellement.

**Reproducibility**
- Observation : Le script DuckDB est montré avec le chemin db/nexamart.duckdb, ce qui indique une exécution locale mais sans README ni script d'installation pour un clone propre.
- Piste d'amélioration : Fournissez un README et des scripts (make run / make check) sans chemins codés en dur pour permettre à un pair de cloner et reproduire en <5 minutes.

## 3. Déclaration d'utilisation de l'IA

> La déclaration documente l'outil utilisé et quand l'IA a été sollicitée, ainsi que des actions de validation basiques. Il manque toutefois une section claire sur les limites ou erreurs observées et les versions/modèles précis de l'outil.

**Sujets bien couverts dans votre déclaration :**

- outils utilisés (nom + version/modèle)
- à quelle étape l'IA a été utilisée
- comment la sortie a été validée par l'humain

**Sujets à ajouter ou expliciter pour la prochaine itération :**

- limites ou erreurs observées

## 4. Pistes d'action pour la prochaine itération

- Reprendre la requête de la section « Preuve » pour qu'elle s'exécute sur `db/nexamart.duckdb` et qu'elle produise la forme attendue (voir pistes en section 1).
- Compléter `ai-usage.md` en y ajoutant : limites ou erreurs observées.

---

## 5. Traçabilité

- **Run ID :** `20260515T122624Z-00a5a04f`
- **Devoir :** `S01`
- **Étudiant·e :** `ches1702`
- **Commit analysé :** `64b6017`
- **Audit (côté instructeur) :** `tools/instructor/feedback_pipeline/audit/20260515T122624Z-00a5a04f/ches1702/`
- **Prompts (SHA-256) :**
  - `sql_extractor_system` : `90ee9e277de7a27f...`
  - `rubric_grader_system` : `505f32d1d8319d66...`
  - `ai_usage_grader_system` : `81cb7fdf89bda55a...`
- **Fournisseur (rubrique) :** `openai`
- **Fournisseur (IA-usage) :** `openai` (gpt-5-mini-2025-08-07)

_Ce feedback a été produit par un pipeline automatisé et **revu par l'équipe pédagogique avant publication**. Aucun chiffre ni étiquette de niveau n'est diffusé à ce stade expérimental : l'objectif est uniquement formatif. Ouvrez une issue dans ce dépôt pour toute question._
