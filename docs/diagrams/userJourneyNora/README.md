# 🗺️ `#26` — User Journey : Nora, Analyste LCB-FT

<a href="../"><img src="../../../assets/images/logo/linceul-audit-logo.webp" align="right" height="64"></a>

![Persona](https://img.shields.io/badge/Persona-Nora_Analyste_LCB--FT-4A90D9?style=flat-square&logo=user&logoColor=white)
![RGPD](https://img.shields.io/badge/RGPD-Art.22_HITL-0055A4?style=flat-square&logo=eu&logoColor=white)
![LCB-FT](https://img.shields.io/badge/LCB--FT-L561--15_CMF-003189?style=flat-square&logo=scales&logoColor=white)
![UX Map](https://img.shields.io/badge/Format-UX_Map-FF6D00?style=flat-square)

---

## Table des Matières

1. [Profil Persona](#1-profil-persona)
2. [Flux Nominal — Alerte Confirmée](#2-flux-nominal--alerte-confirmée)
3. [Flux Alternatif A — Alerte Ignorée](#3-flux-alternatif-a--alerte-ignorée)
4. [Flux Alternatif B — Escalade vers DPO Axel](#4-flux-alternatif-b--escalade-vers-dpo-axel)
5. [Flux Alternatif C — Timeout HITL](#5-flux-alternatif-c--timeout-hitl)
6. [Synthèse des Points de Friction](#6-synthèse-des-points-de-friction)
7. [Diagrammes Liés](#7-diagrammes-liés)

---

## 1. Profil Persona

Attribut | Détail
---|---
**Nom** | Nora
**Âge** | 34 ans
**Rôle** | Analyste Senior LCB-FT
**Objectif principal** | Statuer sur les alertes de fraude dans les délais réglementaires
**Contrainte clé** | Latence UI < 200 ms — SHAP top-3 en langage naturel
**Risque redouté** | Engager sa responsabilité sur une décision algorithmique opaque
**Outil principal** | Interface analyste Linceul Audit (voir `#27`)

---

## 2. Flux Nominal — Alerte Confirmée

> Nora reçoit une alerte critique, analyse les éléments explicatifs, valide la suspicion et transmet à TRACFIN.

# | Étape | Action de Nora | Système | Émotion | Point de Friction
---|---|---|---|---|---
1 | **Réception alerte** | Consulte la file d'alertes, sélectionne l'alerte critique | Notification push + dashboard temps réel | 😐 Neutre | File potentiellement longue — priorisation nécessaire
2 | **Lecture du contexte** | Consulte le score de risque, le montant, la contrepartie, le pays | Interface < 200 ms (US-01) | 🤔 Concentrée | Score sans explication = boîte noire inacceptable
3 | **Analyse SHAP** | Lit le top-3 des features explicatives en langage naturel | `SHAPExplainer` — top-3 (US-07) | 😌 Rassurée | Formulation trop technique → friction cognitive
4 | **Recherche de similarité** | Consulte les transactions similaires via pgvector | `VectorSearchService` — pgvector | 🔍 Analytique | Résultats non pertinents si embeddings dégradés
5 | **Consultation Data Lineage** | Vérifie la provenance des features critiques | Lien contextuel Data Lineage (`#14`) | 🧐 Rigoureuse | Profondeur de traçabilité parfois excessive
6 | **Décision Override HITL** | Clique sur « Confirmer la suspicion » + saisit la justification obligatoire | Log WORM immuable (US-08, US-10) | 😤 Sous pression | Justification obligatoire perçue comme contraignante
7 | **Génération rapport PDF** | Validation → déclenchement automatique du rapport L561-15 | Génération PDF automatique (US-11) | 😮 Soulagée | Délai de génération > 2 s perçu négativement
8 | **Transmission TRACFIN** | Valide l'envoi du rapport au DPO Axel pour signature | Workflow DPO → TRACFIN | 😊 Satisfaite | Dépendance au DPO peut introduire un délai

---

## 3. Flux Alternatif A — Alerte Ignorée

> Nora juge l'alerte non fondée après analyse et la classe sans suite.

# | Étape | Action de Nora | Système | Émotion | Point de Friction
---|---|---|---|---|---
1–4 | **Analyse identique** | Idem flux nominal étapes 1 à 4 | — | 🤔 Concentrée | —
5 | **Décision de clôture** | Clique sur « Classer sans suite » + justification obligatoire | Log WORM immuable (US-08) | 😌 Confiante | Justification exigée même pour un faux positif
6 | **Archivage** | L'alerte est archivée avec horodatage et identité de Nora | Archive WORM + audit trail | 😐 Neutre | Aucun retour de feedback sur la qualité de la décision

---

## 4. Flux Alternatif B — Escalade vers DPO Axel

> Nora identifie un risque juridique ou une ambiguïté réglementaire nécessitant l'arbitrage du DPO.

# | Étape | Action de Nora | Système | Émotion | Point de Friction 
---|---|---|---|---|---
1–4 | **Analyse identique** | Idem flux nominal étapes 1 à 4 | — | 🤔 Concentrée | —
5 | **Identification du risque juridique** | Détecte une ambiguïté (ex. : personne politiquement exposée, double nationalité) | Enrichissement Core Banking | 😰 Incertaine | Manque d'indicateurs réglementaires contextuels
6 | **Escalade** | Clique sur « Escalader vers DPO » + note de contexte | Notification Axel + log WORM | 😤 Frustrée | Perte de visibilité sur le suivi post-escalade
7 | **Attente arbitrage** | Nora est en attente passive — l'alerte est gelée | Statut « En attente DPO » visible | 😕 Anxieuse | Délai d'Axel non borné réglementairement
8 | **Réception décision DPO** | Consulte la décision d'Axel et applique son instruction | Notification retour + log WORM | 😊 Rassurée | Décision d'Axel parfois insuffisamment motivée

---

## 5. Flux Alternatif C — Timeout HITL

> Nora n'a pas statué dans le délai imparti — le système déclenche une escalade automatique.

# | Étape | Action de Nora | Système | Émotion | Point de Friction
---|---|---|---|---|---
1 | **Réception alerte** | Alerte reçue mais non traitée (surcharge, absence) | Dashboard | 😓 Surchargée | File d'alertes trop volumineuse
2 | **Rappel automatique** | Reçoit une notification de rappel (T+1h) | Scheduler — notification push | 😬 Stressée | Rappels répétés perçus comme intrusifs
3 | **Timeout atteint** | Délai maximal dépassé sans décision de Nora | Escalade automatique vers superviseur | 😨 Culpabilisée | Sentiment de perte de contrôle
4 | **Escalade superviseur** | Le superviseur est notifié et hérite de l'alerte | Log WORM — traçabilité complète de l'inaction | 😞 Déresponsabilisée | Stigmatisation possible en cas d'escalades répétées
5 | **Reprise possible** | Nora peut reprendre la main si le superviseur le lui délègue | Réassignation manuelle | 😐 Neutre | Processus de réassignation non formalisé dans l'UI

---

## 6. Synthèse des Points de Friction

Point de Friction | Flux Concerné | Sévérité | Recommandation
---|---|---|---
SHAP trop technique | Nominal | 🔴 Haute | Reformulation NLP systématique en langage métier
Justification Override perçue comme contraignante | Nominal, Alt. A | 🟠 Moyenne | Proposer des justifications pré-remplies (templates)
Délai génération PDF > 2 s | Nominal | 🟠 Moyenne | Génération asynchrone avec indicateur de progression
Perte de visibilité post-escalade | Alt. B | 🔴 Haute | Timeline de suivi visible pour Nora après escalade
Délai DPO non borné | Alt. B | 🔴 Haute | SLA DPO configurable avec relance automatique
File d'alertes non priorisée | Alt. C | 🟠 Moyenne | Tri automatique par score de risque décroissant
Processus de réassignation non formalisé | Alt. C | 🟡 Faible | Bouton « Reprendre » explicite dans l'UI

---

## 7. Diagrammes Liés

# | Diagramme | Relation 
---|---|---
`#06` 🧑‍⚖️ Séquence — Flux HITL (alerte critique) | Modélisation technique du flux Override HITL que Nora exécute à l'étape 6 | Complémentaire
`#08` 📋 BPMN — Processus LCB-FT / Déclaration de soupçon | Processus métier formalisé dont ce User Journey est la vue subjective | Complémentaire
`#25` 👤 Use Cases UML — par Persona | Cas d'utilisation formels associés aux actions de Nora | Contextuel
`#27` 🖥️ Wireframe — Interface Analyste (Nora) | Matérialisation UI des étapes de ce parcours | Dépendant
`#10` 🧵 État — Virtual Thread (Pinning JNI) | Contrainte technique garantissant la latence < 200 ms exigée par Nora | Contextuel