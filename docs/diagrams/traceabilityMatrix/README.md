# **Matrice de traçabilité**<a href="../"><img src="../../../assets/images/logo/linceul-audit-logo.webp" align="right" height="64"></a>
<div align="center">

[![Tests](https://img.shields.io/badge/User%20Stories-US--01%20→%20US--15-blue?style=flat&logo=testinglibrary&logoColor=white)](.) 
[![JUnit](https://img.shields.io/badge/JUnit-5-25A162?style=flat&logo=junit5&logoColor=white)](.) 
[![pytest](https://img.shields.io/badge/pytest-3.13-0A9EDC?style=flat&logo=pytest&logoColor=white)](.) 
[![Gatling](https://img.shields.io/badge/Gatling-Charge-FF9E00?style=flat&logo=gatling&logoColor=white)](.) 
[![Classification](https://img.shields.io/badge/Classification-CONFIDENTIEL-red?style=flat)](.)

</div>

> Ce document est le **document de clôture juridique** du projet.  
> Il cartographie exhaustivement chaque User Story (US-01 à US-15) avec ses cas de test associés,  
> ses critères d'acceptation mesurables, le persona validant et la réglementation couverte.  
> Tout statut `⬜ À faire` non résolu avant mise en production constitue une non-conformité bloquante.

---

## Légende

Symbole | Signification
:-:|---
⬜ | À implémenter
🔄 | En cours
✅ | Validé
🔴 | Bloquant — non conforme
`U` | Test Unitaire
`I` | Test d'Intégration
`P` | Test de Parité Python/Java
`C` | Test de Charge
`R` | Test de Résilience
`S` | Test de Sécurité
`M` | Test MLOps / Dérive

---

## Épic 1 — Ingestion & Prétraitement

<details open>
<summary><b>US-01 · US-02 · US-03</b></summary>

Champ | US-01 — Intégrité | US-02 — Enrichissement | US-03 — Imputation
---|---|---|---
**Titre** | Valider le schéma JSON | Enrichir via données historiques | Imputer les valeurs manquantes
**Type de test** | `U` `I` | `U` `C` | `U`
**Framework** | JUnit 5 | JUnit 5 · Gatling | pytest
**Critère d'acceptation** | 0 transaction au format invalide ne dépasse la validation · Rejet HTTP 400 immédiat | Latence d'enrichissement < 10 ms au P99 sous charge nominale | 0 valeur `NaN` / `Null` atteignant le moteur d'inférence · Stratégie documentée par champ
**Cas de test nominal** | `TC-01-N` : transaction valide → acceptée | `TC-02-N` : enrichissement < 10 ms | `TC-03-N` : champ manquant → imputation médiane
**Cas de test limite** | `TC-01-L` : champ `montant` null → rejeté | `TC-02-L` : BDD indisponible → fallback cache Redis | `TC-03-L` : champ inconnu → imputation -1
**Cas de test adversarial** | `TC-01-A` : injection SQL dans JSON → rejeté | — | `TC-03-A` : 100% champs nuls → rejet transaction
**Persona validant** | Nora · Sophie | Sophie | Ezra
**Réglementation** | AI Act Art. 9 (robustesse) · RGPD Art. 32 | CMF L561-15 (performance) | ISO/IEC 42001 (qualité données)
**Statut** | ⬜ | ⬜ | ⬜

</details>

---

## Épic 2 — Inférence & Détection

<details open>
<summary><b>US-04 · US-05 · US-06</b></summary>

Champ | US-04 — Performance | US-05 — Fail-Safe | US-06 — Parité 
---|---|---|---
**Titre** | Inférence ONNX dans pool dédié | Bascule automatique mode dégradé | Parité score Python = score Java
**Type de test** | `U` `C` | `R` `I` | `P`
**Framework** | JUnit 5 · Gatling · JFR | JUnit 5 | `tests/parity/` (pytest + JUnit 5)
**Critère d'acceptation** | Latence inférence < 1 ms au P99 · 0 Virtual Thread pinné (JFR `jdk.VirtualThreadPinned` = 0) | Bascule Rule Engine si latence > 500 ms · Continuité de service 100% · Log de dégradation généré | Delta score \|Java − Python\| < 1e-5 sur Golden Dataset (100% des cas)
**Cas de test nominal** | `TC-04-N` : inférence nominale < 1 ms | `TC-05-N` : système stable → pas de bascule | `TC-06-N` : même input → delta < 1e-5
**Cas de test limite** | `TC-04-L` : 5× charge nominale → 0 pinning | `TC-05-L` : latence = 501 ms → bascule déclenchée | `TC-06-L` : valeurs extrêmes (min/max) → delta < 1e-5
**Cas de test adversarial** | `TC-04-A` : 10× charge → graceful degradation | `TC-05-A` : ONNX crash total → Rule Engine actif | `TC-06-A` : opérateurs ONNX non supportés → erreur explicite
**Persona validant** | Sophie · Ezra | Sophie | Ezra
**Réglementation** | — (exigence technique) | CMF L561-15 (continuité de service) | AI Act (reproductibilité) · ISO/IEC 42001
**Statut** | ⬜ | ⬜ | ⬜

</details>

---

## Épic 3 — Interface Analyste & Décision HITL

<details open>
<summary><b>US-07 · US-08 · US-09</b></summary>

Champ | US-07 — Explicabilité | US-08 — Décision HITL | US-09 — Similitude 
---|---|---|---
**Titre** | Afficher SHAP top-3 en langage naturel | Qualifier une alerte avec Override | Transactions similaires via pgvector
**Type de test** | `U` `I` | `I` | `U` `I`
**Framework** | pytest · JUnit 5 | JUnit 5 | JUnit 5
**Critère d'acceptation** | 3 facteurs SHAP affichés en < 200 ms · Libellé compréhensible par un non-technicien | Override enregistré avec justification obligatoire · Log WORM généré immédiatement · Impossible de valider sans justification | Liste de similarité retournée en < 200 ms · Pertinence vérifiée sur 10 transactions de référence
**Cas de test nominal** | `TC-07-N` : alerte générée → SHAP top-3 affiché | `TC-08-N` : analyste qualifie avec justification | `TC-09-N` : transaction → 5 similaires retournées
**Cas de test limite** | `TC-07-L` : SHAP non calculable → message explicatif | `TC-08-L` : soumission sans justification → bloqué | `TC-09-L` : 0 similaire trouvé → message explicite
**Cas de test adversarial** | `TC-07-A` : valeurs SHAP nulles → fallback textuel | `TC-08-A` : tentative bypass HITL → rejeté et loggé | `TC-09-A` : embedding corrompu → erreur contrôlée
**Persona validant** | Nora | Nora · Axel | Nora
**Réglementation** | RGPD Art. 22 (HITL explicable) · AI Act | RGPD Art. 22 · CMF L561-15 | — (exigence fonctionnelle)
**Statut** | ⬜ | ⬜ | ⬜

</details>

---

## Épic 4 — Audit & Conformité

<details open>
<summary><b>US-10 · US-11 · US-12</b></summary>

Champ | US-10 — Traçabilité | US-11 — Reporting | US-12 — Droit d'accès
---|---|---|---
**Titre** | Log WORM cryptographique | Pré-rapport PDF L561-15 | Extraction historique RGPD
**Type de test** | `U` `S` | `I` | `I`
**Framework** | JUnit 5 | JUnit 5 | JUnit 5
**Critère d'acceptation** | Log généré pour chaque décision · Hash SHA-256 valide · Immutabilité vérifiée (tentative de modification → échec) · Rejeu déterministe à 5 ans | PDF généré conforme Art. L561-15 · SHAP values incluses · Numéro de version du modèle présent | Extraction complète en réponse dans le délai légal · Format lisible · Données pseudonymisées
**Cas de test nominal** | `TC-10-N` : décision → log WORM généré + hash valide | `TC-11-N` : alerte confirmée → PDF généré | `TC-12-N` : demande RGPD → extraction complète
**Cas de test limite** | `TC-10-L` : tentative modification log → rejetée + alerte | `TC-11-L` : SHAP indisponible → PDF avec mention | `TC-12-L` : aucune donnée → réponse vide conforme
**Cas de test adversarial** | `TC-10-A` : corruption stockage WORM → détection + alerte | `TC-11-A` : données manquantes → génération partielle conforme | `TC-12-A` : demande pour utilisateur inexistant → 404 contrôlé
**Persona validant** | Axel | Nora · Axel | Axel
**Réglementation** | CMF L561-15 · ISO/IEC 42001 (AIMS) | CMF L561-15 | RGPD Art. 12 · Art. 15
**Statut** | ⬜ | ⬜ | ⬜

</details>

---

## Épic 5 — Sécurité & MLOps

<details open>
<summary><b>US-13 · US-14 · US-15</b></summary>

Champ | US-13 — Sécurité Modèle | US-14 — Anti-Adversaire | US-15 — Dérive
---|---|---|---
**Titre** | Chiffrer le modèle `.onnx` au repos | Détecter les inputs adversariaux | Alerter si dérive distribution > 5%
**Type de test** | `S` | `S` `U` | `M` `I`
**Framework** | JUnit 5 · script shell | pytest · JUnit 5 | pytest (PSI · KL-Divergence)
**Critère d'acceptation** | Fichier `.onnx` illisible sans clé KMS · Chiffrement AES-256 vérifié · Aucun accès sans authentification KMS | Input atypique détecté et mis en quarantaine · Log forensique généré · Taux de faux positifs < 1% | Alerte déclenchée si PSI > 0.2 ou déviation > 5% · Pipeline re-entraînement activé automatiquement
**Cas de test nominal** | `TC-13-N` : lecture avec clé valide → succès | `TC-14-N` : input normal → accepté | `TC-15-N` : distribution stable → aucune alerte
**Cas de test limite** | `TC-13-L` : lecture sans clé → accès refusé + log | `TC-14-L` : input à la frontière → score de confiance affiché | `TC-15-L` : dérive exactement 5% → alerte déclenchée
**Cas de test adversarial** | `TC-13-A` : tentative extraction clé depuis code → KMS bloque | `TC-14-A` : attaque FGSM simulée → détectée + quarantaine | `TC-15-A` : injection données empoisonnées → détection + alerte
**Persona validant** | Ezra · Axel | Sophie | Ezra · Sophie
**Réglementation** | RGPD Art. 32 · AI Act (intégrité modèle) | AI Act Art. 9 (robustesse) · RGPD Art. 32 | AI Act (surveillance continue) · ISO/IEC 42001
**Statut** | ⬜ | ⬜ | ⬜

</details>

---

## Récapitulatif global

US | Titre court | Épic | Types | Framework principal | Persona | Statut
---|---|---|---|---|---|---
US-01 | Valider schéma JSON | 1 | `U` `I` | JUnit 5 | Nora · Sophie | ⬜
US-02 | Enrichir < 10 ms | 1 | `U` `C` | JUnit 5 · Gatling | Sophie | ⬜
US-03 | Imputer NaN/Null | 1 | `U` | pytest | Ezra | ⬜
US-04 | Inférence ONNX pool dédié | 2 | `U` `C` | JUnit 5 · JFR | Sophie · Ezra | ⬜
US-05 | Bascule Rule Engine | 2 | `R` `I` | JUnit 5 | Sophie | ⬜
US-06 | Parité Python/Java | 2 | `P` | pytest + JUnit 5 | Ezra | ⬜
US-07 | SHAP top-3 naturel | 3 | `U` `I` | pytest · JUnit 5 | Nora | ⬜
US-08 | Override HITL | 3 | `I` | JUnit 5 | Nora · Axel | ⬜
US-09 | Similitude pgvector | 3 | `U` `I` | JUnit 5 | Nora | ⬜
US-10 | Log WORM cryptographique | 4 | `U` `S` | JUnit 5 | Axel | ⬜
US-11 | Rapport PDF L561-15 | 4 | `I` | JUnit 5 | Nora · Axel | ⬜
US-12 | Extraction RGPD | 4 | `I` | JUnit 5 | Axel | ⬜
US-13 | Chiffrement `.onnx` | 5 | `S` | JUnit 5 · shell | Ezra · Axel | ⬜
US-14 | Détection adversariale | 5 | `S` `U` | pytest · JUnit 5 | Sophie | ⬜
US-15 | Alerte dérive > 5% | 5 | `M` `I` | pytest (PSI) | Ezra · Sophie | ⬜

---

<div align="center">

[← Diagrammes](../) · [Use Cases](../useCase) · [Documentation](../../)

___
© 2026 - Projet Linceul Audit. Tous droits réservés.
</div>