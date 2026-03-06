# **Matrice de conformité**<a href="../../"><img src="../../../assets/images/logo/linceul-audit-logo.webp" align="right" height="64"></a>
<div align="center">

[![AI Act](https://img.shields.io/badge/AI%20Act-UE%202024%2F1689-003399?style=flat&logo=europeancentralbank&logoColor=white)](../../../docs/compliance/FRIA.md)
[![RGPD](https://img.shields.io/badge/RGPD-Art.%2022%20%7C%2032%20%7C%2035-003399?style=flat&logo=europeancentralbank&logoColor=white)](../../../docs/compliance/DPIA.md)
[![CMF](https://img.shields.io/badge/CMF-L561--15-003399?style=flat)](.)
[![ISO](https://img.shields.io/badge/ISO%2FIEC-42001%20AIMS-lightgrey?style=flat)](.)
[![Diagramme](https://img.shields.io/badge/Diagramme-%2324-blueviolet?style=flat)](.)
[![Classification](https://img.shields.io/badge/Classification-CONFIDENTIEL-red?style=flat)](.)

</div>

> **Objet :** Mapping exhaustif des 15 User Stories (US-01 à US-15) contre les 4 cadres réglementaires applicables au projet LINCEUL-2026.  
> Ce document constitue une **pièce du bouclier juridique** opposable à l'ACPR et à la CNIL.  
> Il doit être mis à jour à chaque modification du périmètre fonctionnel.

---

## Légende

Symbole | Signification
:-:|---
🟢|Implémente directement et satisfait l'exigence réglementaire
🔗|Contribue à / supporte l'exigence (implémentation partielle)
⬜|Sans relation directe
🔺|Exigence **critique** — non-conformité = risque juridique immédiat

---

## 1. Matrice US ↔ Réglementations

User Story | Description synthétique | AI Act | RGPD | CMF L561-15 | ISO/IEC 42001
:-:|---|:-:|:-:|:-:|:-:
**US-01** | Validation schéma JSON — rejet des formats invalides | 🟢 Art.10 | 🔗 Art.5(1)(d) | ⬜ | 🟢 §8.2
**US-02** | Enrichissement historique < 10ms | 🔗 Art.15 | ⬜ | ⬜ | 🔗 §8.4
**US-03** | Imputation NaN/Null selon stratégie documentée | 🟢 Art.10 | 🔗 Art.5(1)(d) | ⬜ | 🟢 §8.2
**US-04** | Inférence dans pool dédié — sans blocage Virtual Threads | 🔗 Art.15 | ⬜ | 🔗 Continuité | 🟢 §8.4
**US-05** | Bascule automatique Rule Engine si latence > 500ms | 🔗 Art.15 | ⬜ | 🔺 Continuité service | 🟢 §8.6
**US-06** | Parité Python/Java — score delta < ε (1e-5) | 🔺 Art.15 | ⬜ | 🔺 Déterminisme | 🟢 §8.4
**US-07** | SHAP top-3 en langage naturel | 🔺 Art.13 | 🔺 Art.22(3) | ⬜ | 🟢 §6.2
**US-08** | Override HITL avec justification obligatoire | 🔺 Art.14 | 🔺 Art.22(2)(b) | ⬜ | 🟢 §8.5
**US-09** | Similarité pgvector — transactions passées analogues | 🔗 Art.13 | 🔗 Art.22(3) | ⬜ | 🔗 §8.5
**US-10** | Log cryptographique immuable WORM par décision | 🔺 Art.12 | 🔗 Art.33 | 🔺 Art.L561-15 | 🔺 §9.1
**US-11** | Pré-rapport PDF conforme L561-15 (Tracfin) | ⬜ | ⬜ | 🔺 Art.L561-15 | 🔗 §9.1
**US-12** | Extraction historique — droits d'accès RGPD | ⬜ | 🔺 Art.15 | ⬜ | ⬜
**US-13** | Chiffrement AES-256 du fichier .onnx au repos | 🔗 Art.9 | 🔺 Art.32 | ⬜ | 🟢 §8.3
**US-14** | Détection inputs adversariaux — attaques Évasion | 🔺 Art.15 | 🔗 Art.32 | ⬜ | 🔺 §6.1
**US-15** | Alerte automatique si dérive score > 5% | 🔺 Art.9 | ⬜ | ⬜ | 🔺 §9.1

---

## 2. Détail par cadre réglementaire

### 🇪🇺 AI Act (Règlement UE 2024/1689)

<details>
<summary>Voir le détail des articles applicables</summary>

Article | Exigence | User Stories | Statut
---|---|---|:-:
**Art. 9** — Système de gestion des risques | Identification, analyse et atténuation des risques tout au long du cycle de vie | US-13 · US-14 · US-15 | 🔺 Critique
**Art. 10** — Gouvernance des données | Pratiques de gestion des données d'entraînement, validation, test — absence de biais | US-01 · US-03 | 🟢
**Art. 12** — Tenue de registres | Conservation des logs permettant la surveillance post-déploiement | US-10 | 🔺 Critique
**Art. 13** — Transparence | Information compréhensible aux déployeurs et utilisateurs | US-07 · US-09 | 🔺 Critique
**Art. 14** — Supervision humaine | Mesures permettant la surveillance et l'intervention humaine | US-08 | 🔺 Critique
**Art. 15** — Exactitude, robustesse, cybersécurité | Performances maintenues — résistance aux attaques adversariales | US-02 · US-04 · US-05 · US-06 · US-14 | 🔺 Critique
**Art. 27** — FRIA | Évaluation d'impact sur les droits fondamentaux | Transverse | 🔺 Obligatoire
**Annexe III §5(b)** | Exception fraude financière (hors scoring de crédit) | Architecture globale | 🟢 Documenté

</details>

---

### 🛡️ RGPD (Règlement UE 2016/679)

<details>
<summary>Voir le détail des articles applicables</summary>

Article | Exigence | User Stories | Statut
---|---|---|:-:
**Art. 5(1)(d)** — Exactitude | Données exactes et à jour — rectification sans délai | US-01 · US-03 | 🟢
**Art. 15** — Droit d'accès | Extraction de l'historique de traitement sur demande | US-12 | 🔺 Critique
**Art. 22** — Décision automatisée | Droit de ne pas faire l'objet d'une décision automatisée — HITL | US-07 · US-08 · US-09 | 🔺 Critique
**Art. 25** — Privacy by Design | Protection des données dès la conception | US-13 | 🟢
**Art. 32** — Sécurité du traitement | Mesures techniques garantissant l'intégrité et la confidentialité | US-13 · US-14 | 🔺 Critique
**Art. 33** — Notification de violation | Notification CNIL sous 72h en cas de violation | US-10 (preuve) | 🔗
**Art. 35** — DPIA | Évaluation d'impact sur la protection des données (PIA) | Transverse | 🔺 Obligatoire 

</details>

---

### 🏦 CMF — Code Monétaire et Financier

<details>
<summary>Voir le détail des articles applicables</summary>

Article | Exigence | User Stories | Statut
---|---|---|:-:
**Art. L561-15** — Déclaration de soupçon | Obligation de déclaration à Tracfin avec justification | US-10 · US-11 | 🔺 Critique
**Art. L561-15** — Traçabilité | Conservation des logs 5 ans — rejeu déterministe du modèle exact | US-06 · US-10 | 🔺 Critique
**Art. L561-15** — Continuité | Maintien du service de surveillance même en cas de dégradation | US-05 | 🔺 Critique

</details>

---

### 📋 ISO/IEC 42001 — AIMS (AI Management System)

<details>
<summary>Voir le détail des clauses applicables</summary>

Clause | Exigence | User Stories | Statut
---|---|---|:-:
**§6.1** — Risques et opportunités | Évaluation des risques liés aux systèmes IA | US-13 · US-14 | 🔺 Critique
**§6.2** — Objectifs IA | Documentation des objectifs de performance et d'équité | US-07 | 🟢
**§8.2** — Gouvernance des données | Qualité des données d'entraînement et d'inférence | US-01 · US-03 | 🟢
**§8.3** — Cycle de vie du système | Contrôles tout au long du développement et déploiement | US-13 | 🟢
**§8.4** — Opérations | Maîtrise opérationnelle du système IA en production | US-02 · US-04 · US-06 | 🟢
**§8.5** — Impacts humains | Évaluation des impacts sur les individus | US-08 · US-09 | 🟢
**§8.6** — Résilience | Continuité et résilience du système | US-05 | 🟢
**§9.1** — Surveillance et mesure | Monitoring des performances, détection de dérive | US-10 · US-11 · US-15 | 🔺 Critique

</details>

---

## 3. Synthèse des risques de non-conformité

Criticité | User Stories | Risque en cas de non-implémentation
---|---|---
🔴 **Bloquant** | US-06, US-07, US-08, US-10, US-11 | Mise en demeure ACPR / CNIL — suspension d'activité
🟠 **Majeur**   | US-01, US-03, US-13, US-14, US-15 | Amende RGPD (4% CA mondial) — sanctions AI Act
🟡 **Modéré**   | US-02, US-04, US-05, US-09, US-12 | Non-conformité partielle — risque lors d'un audit

---

## 4. Traçabilité documentaire

Document | Lien | Réglementations couvertes
---|---|---
FRIA (Fundamental Rights Impact Assessment) | [`docs/compliance/FRIA.md`](../../compliance/FRIA.md) | AI Act Art. 27
DPIA (Data Protection Impact Assessment) | [`docs/compliance/DPIA.md`](../../compliance/DPIA.md) | RGPD Art. 35
Model Cards | [`docs/compliance/model-cards/`](../../compliance/model-cards/) | ISO/IEC 42001 §8.3
Stratégie d'imputation | [`docs/dataops/imputation-strategy.md`](../../dataops/imputation-strategy.md) | AI Act Art. 10
Feature Store Registry | [`docs/dataops/feature-store-registry.md`](../../dataops/feature-store-registry.md) | CMF L561-15

___

<div align="center">

[← Diagrammes](../) · [Documentation](../../)<!-- · [FRIA](../../compliance/FRIA.md) · [DPIA](../../compliance/DPIA.md)-->

___
© 2026 - Projet Linceul Audit. Tous droits réservés. · `#24` ⚖️ Matrice de Conformité Réglementaire
</div>