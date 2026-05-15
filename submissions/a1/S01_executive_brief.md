# Board Brief — S01

## Question du CEO

Quelles catégories de produits
déclinent, dans quelles régions, et
pourquoi ?

## Réponse exécutive

Les ventes par téléphone de la catégorie de produit Automotive en Alberta ont connu la forte  baisse en Q4 2025
Les ventes  en ligne de la catégorie de produit Pet Supplies  en BC ont connu la plus forte  baisse en Q2 2025
Les ventes  par téléphone de la catégorie de produit Toys & Games en Estrie ont connu la plus forte baisse  en Q3 2025
Les ventes  en ligne a partir de MarketPlace de la catégorie de produit  Pet Supplies en Ontario ont connu la plus forte baisse  en Q2 2025
Les ventes  par téléphone de la catégorie de produit  Toys & Games en Outaouais ont connu la plus forte baisse  en Q4 2025
Les ventes  en  magasins  de la catégorie de produit   Pet Supplies au  Québec ont connu la plus forte baisse  en Q3 2025

Les raisons des baisses de ventes ne sont pas du au retour suite a une analyse que j'ai demande a claude de le faire en utilisant les données de la table  raw_fact_returns

## Décisions de modélisation

@ches1702 ➜ /workspaces/onboarding-gis805-v2-ches1702 (main) $ duckdb db/nexamart.duckdb << 'EOF'
SELECT
  p.category,
  s.region,
  f.channel_id,
  d.year,
  d.quarter,
  SUM(f.line_total) AS total_sales,
  SUM(f.quantity) AS total_quantity
FROM raw_fact_sales f
JOIN raw_dim_product p ON f.product_id = p.product_id
JOIN raw_dim_store s ON f.store_id = s.store_id
JOIN raw_dim_date d ON f.order_date = d.date_key
GROUP BY p.category, s.region, f.channel_id, d.year, d.quarter
ORDER BY p.category, s.region, f.channel_id, d.year, d.quarter, total_sales ASC;
EOF

## Preuve


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
┌───────────┬──────────────┬──────────┬───────┬─────────┬────────────────┐
│  region   │   category   │ channel  │ year  │ quarter │ decline_amount │
│  varchar  │   varchar    │ varchar  │ int64 │  int64  │     double     │
├───────────┼──────────────┼──────────┼───────┼─────────┼────────────────┤
│ Alberta   │ Automotive   │ CH-PHONE │  2025 │       4 │       -1529.25 │
│ BC        │ Pet Supplies │ CH-WEB   │  2025 │       2 │       -3306.08 │
│ Estrie    │ Toys & Games │ CH-PHONE │  2025 │       3 │       -1582.87 │
│ Ontario   │ Pet Supplies │ CH-MKTPL │  2025 │       2 │       -3339.25 │
│ Outaouais │ Toys & Games │ CH-PHONE │  2025 │       4 │       -1361.15 │
│ Québec    │ Pet Supplies │ CH-STORE │  2025 │       3 │       -1822.04 │
└───────────┴──────────────┴──────────┴───────┴─────────┴────────────────┘


## Validation



## Risques / limites
Le modèle se base seulement sur une analyse des ventes en 2025

## Prochaine recommandation
Afin de déterminer les véritables causes des baisses de ventes observées dans les catégories de produits identifiées par région, il sera nécessaire d’intégrer l’ensemble des données de ventes des années 2023 et 2024.

