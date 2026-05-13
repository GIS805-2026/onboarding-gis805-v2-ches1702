# Trace d'usage IA — GIS805

> Chaque interaction significative avec un outil IA doit être documentée ici.
> Ce fichier est **obligatoire** et évalué à chaque remise.

## Format par entrée

```
### 2026-05-12 — Séance S01
- **Modèle :** GitHub Copilot CLI
- **Prompt :** « Quel est le produit et le canal de vente qui a connu la plus grosse baisse de revenu par région en 2025 ? Je veux voir pour chaque région : la catégorie de produit, le canal, le trimestre et le montant de la baisse.
Utilise la bd suivante duckdb db/nexamart.duckdb ainsi que les tables suivantes raw_fact_sales ,raw_dim_product, raw_dim_store , raw_dim_date
Génere moi le requête SQL»
- **Résultat :** Copilot a généré la requete SQL
- **Validation :** (J'ai exécuté la requête SQL dans le Terminal )
- **Justification :** première itération des livrables qu'on doit remettre chaque Séance
```

---

### 2026-01-XX — Séance S00 *(exemple — supprimez cette entrée quand vous ajoutez les vôtres)*
- **Modèle :** GitHub Copilot Chat
- **Prompt :** « Qu'est-ce qui se trouve dans mon dépôt ? Explique-moi la structure du projet. »
- **Résultat :** Copilot a listé les dossiers principaux (sql/, answers/, data/, docs/) et expliqué le rôle de chacun dans le contexte d'un entrepôt dimensionnel.
- **Validation :** J'ai comparé la réponse avec le README.md et le contenu réel des dossiers — tout correspondait.
- **Justification :** Première prise de contact avec le dépôt ; je voulais comprendre l'organisation avant de lancer les commandes.

<!-- Ajoutez vos entrées ci-dessous -->
### 2026-05-12 — Séance S01
- **Modèle :** GitHub Copilot CLI
- **Prompt :** « Quel est le produit et le canal de vente qui a connu la plus grosse baisse de revenu par région en 2025 ? Je veux voir pour chaque région : la catégorie de produit, le canal, le trimestre et le montant de la baisse.
Utilise la bd suivante duckdb db/nexamart.duckdb ainsi que les tables suivantes raw_fact_sales ,raw_dim_product, raw_dim_store , raw_dim_date
Génere moi le requête SQL»
- **Résultat :** Copilot a généré la requete SQL
- **Validation :** (J'ai exécuté la requête SQL dans le Terminal )
- **Justification :** première itération des livrables qu'on doit remettre chaque Séance
### 2026-05-12 — Séance S01
- **Modèle :** GitHub Copilot CLI
- **Prompt :** « Si tu ajoutes la table raw_fact_return aux résultats obtenus par la requête SQL que tu as générée, est-ce que les retours produits deviennent la principale raison des baisses de ventes observées ?»
- **Résultat :** Observations clés (dans le contexte Kimball) :

Le taux de retour (return_rate_percent) est souvent à 0 % ou très faible (< 50 %), ce qui suggère que les retours ne sont pas la cause principale des baisses de ventes observées dans votre tableau initial.
Les changements de retours (returns_change) varient, mais ne corrèlent pas systématiquement avec les baisses de revenu les plus fortes (ex. : pour Alberta/Automotive, returns_change est 0 ou positif, mais la baisse est ailleurs).
Cela indique que les causes des déclins pourraient être externes (concurrence, saisonnalité) plutôt qu'internes (retours dus à la qualité).
- **Validation :** la raison des baises de ventes ne sont pas reliés aux retour de produit 
- **Justification :** première itération des livrables qu'on doit remettre chaque Séance