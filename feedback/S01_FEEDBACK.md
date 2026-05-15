# Rétroaction automatisée -- S01 (Diagnostic fondamental -- NexaMart kickoff)

_Générée le 2026-05-14T22:24:44+00:00 -- Run `20260514T221333Z-7d34bf6a`_

Ce document est produit par un pipeline reproductible (vérification SQL déterministe + analyse LLM du brief et de la déclaration IA). Une revue humaine précède toujours sa publication. **À ce stade expérimental, aucune note ni étiquette de niveau n'est diffusée : l'objectif est purement formatif.**

---

## 1. Vérification automatique de la requête SQL

La requête extraite de votre brief n'a pas pu être validée automatiquement. Quelques pistes constructives ci-dessous pour vous aider à la rendre exécutable et alignee avec la question posée.

_Observation technique : aucun bloc SQL fencé trouvé et extraction LLM échouée_


**Pistes :**
> Aucun bloc ```sql ... ``` détecté. Encadrez votre requête finale dans la section « Preuve » pour fiabiliser l'auto-validation.
> Extracteur LLM : Le brief fourni ne contient aucune requête SQL ; je ne peux pas extraire une requête principale qui n'existe pas.

## 2. Rétroaction pédagogique sur le brief

> Le brief soumis est essentiellement vide : les sections clés (question CEO, modèle, preuves et recommandations) ne contiennent pas d'information exploitable. Remplissez d'abord la réponse exécutive et la décision demandée, puis ajoutez la preuve SQL et les checks pour soutenir votre recommandation.

### Observations par dimension

**Model quality**
- Observation : Le brief ne décrit aucun modèle dimensionnel ni grain — sections « Décisions de modélisation » et « Question du CEO » vides.
- Piste d'amélioration : Définir explicitement le grain (ex. : ligne de commande) et lister les dimensions et mesures clés avec justification business.

**Validation quality**
- Observation : Aucune requête SQL ou contrôles de qualité fournis dans la section « Preuve » ou « Validation ».
- Piste d'amélioration : Fournir une requête SQL de validation (ex. agrégation par catégorie/région/trimestre) et au moins deux checks (NULLs, doublons).

**Executive justification**
- Observation : La section « Réponse exécutive » est vide ; aucune recommandation décisionnelle n'est formulée pour le CEO.
- Piste d'amélioration : Rédiger un résumé exécutif de 150–300 mots qui répond à la question du CEO et propose une décision ou recommandation claire.

**Process trace**
- Observation : Aucune trace de processus, commit git ou note sur l'utilisation d'IA n'est fournie dans le brief.
- Piste d'amélioration : Ajouter un journal de décision et lister les commits git (≥1 minimum) ainsi que toute aide IA utilisée et la validation humaine effectuée.

**Reproducibility**
- Observation : Aucun README, script de reproduction ou instruction pour exécuter les checks n'est fourni.
- Piste d'amélioration : Inclure un README minimal et un script 'make check' décrivant comment cloner, charger les données et exécuter les contrôles pour reproduire les résultats.

_Quelques points appellent une attention particulière lors de la prochaine itération : sections_vides._

## 3. Déclaration d'utilisation de l'IA

> Le fichier soumis ne contient que le gabarit/exemple et aucune entrée propre à votre travail. Remplissez les entrées réelles en indiquant pour chaque interaction l'outil précis (avec version/modèle), l'étape du projet, la validation humaine effectuée et toute limite observée.

**Sujets à ajouter ou expliciter pour la prochaine itération :**

- outils utilisés (nom + version/modèle)
- à quelle étape l'IA a été utilisée
- comment la sortie a été validée par l'humain
- limites ou erreurs observées

## 4. Pistes d'action pour la prochaine itération

- Reprendre la requête de la section « Preuve » pour qu'elle s'exécute sur `db/nexamart.duckdb` et qu'elle produise la forme attendue (voir pistes en section 1).
- Réviser le brief en tenant compte des observations par dimension de la section 2.
- Compléter `ai-usage.md` en y ajoutant : outils utilisés (nom + version/modèle).
- Compléter `ai-usage.md` en y ajoutant : à quelle étape l'IA a été utilisée.
- Compléter `ai-usage.md` en y ajoutant : comment la sortie a été validée par l'humain.
- Compléter `ai-usage.md` en y ajoutant : limites ou erreurs observées.

---

## 5. Traçabilité

- **Run ID :** `20260514T221333Z-7d34bf6a`
- **Devoir :** `S01`
- **Étudiant·e :** `ches1702`
- **Commit analysé :** `0a1c6ac`
- **Audit (côté instructeur) :** `tools/instructor/feedback_pipeline/audit/20260514T221333Z-7d34bf6a/ches1702/`
- **Prompts (SHA-256) :**
  - `sql_extractor_system` : `90ee9e277de7a27f...`
  - `rubric_grader_system` : `505f32d1d8319d66...`
  - `ai_usage_grader_system` : `81cb7fdf89bda55a...`
- **Fournisseur (rubrique) :** `openai`
- **Fournisseur (IA-usage) :** `openai` (gpt-5-mini-2025-08-07)

_Ce feedback a été produit par un pipeline automatisé et **revu par l'équipe pédagogique avant publication**. Aucun chiffre ni étiquette de niveau n'est diffusé à ce stade expérimental : l'objectif est uniquement formatif. Ouvrez une issue dans ce dépôt pour toute question._
