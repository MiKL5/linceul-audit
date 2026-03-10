# Diagrammesa<a href="../"><img src="../../assets/images/logo/linceul-audit-logo.webp" align="right" height="64"></a>

## Table des Matières

1. [Contexte & Vision](#1-contexte--vision)
2. [Exigences Fonctionnelles](#2-exigences-fonctionnelles)
3. [Architecture Logicielle](#3-architecture-logicielle)
4. [Modèle de Données](#4-modèle-de-données)
5. [Flux Métier & Traitements](#5-flux-métier--traitements)
6. [Concurrence JVM & Résilience](#6-concurrence-jvm--résilience)
7. [Sécurité & Cyber-Résilience](#7-sécurité--cyber-résilience)
8. [MLOps & Cycle de Vie des Modèles](#8-mlops--cycle-de-vie-des-modèles)
9. [Conformité & Gouvernance](#9-conformité--gouvernance)
10. [Interface Utilisateur](#10-interface-utilisateur)
11. [Qualité & Tests](#11-qualité--tests)

---

## 1. Contexte & Vision

Linceul Audit opère un changement de paradigme épistémologique : passer d'une détection de fraude
probabiliste opaque (« Boîte Noire ») à une approche déterministe, explicable et
**juridiquement opposable** (« Boîte Grise »).

`#01` 🗺️ [C4 — Contexte Système (L1)](systemContext/)  
Vue macro : acteurs (Nora, Ezra, Axel, Sophie), systèmes externes (Core Banking, ACPR, CNIL, TRACFIN).  
Point d'entrée sémantique de toute la documentation.

<details>
<summary>ℹ️ Légende des symboles utilisés dans tous les diagrammes C4</summary>

* 🟦 **Système principal** — Linceul Audit
* 🟩 **Acteur humain** — Personas internes
* 🟥 **Système externe** — Core Banking, ACPR, CNIL, TRACFIN
* ⬛ **Stockage** — PostgreSQL, WORM Archive

</details>

---

## 2. Exigences Fonctionnelles

Définition des cas d'usage par persona et des parcours utilisateurs critiques, fondements de l'architecture HITL imposée par le RGPD Art. 22.

[`#25` 👤 Use Cases UML — par Persona](useCase/)  
Cas d'utilisation distincts pour Nora (Analyste LCB-FT), Ezra (Data Scientist), Axel (DPO), Sophie (SRE).  
Matrice des responsabilités fonctionnelles.

[`#26` 🗺️ User Journey — Nora (Analyste LCB-FT)](userJourneyNora/)  
Parcours cognitif complet : réception alerte → consultation SHAP → similarité pgvector → Override HITL → génération rapport PDF L561-15.

<details>
<summary>📋 Récapitulatif des 4 Personas</summary>

Persona|Rôle|Exigence clé
---|---|---
**Nora**, 34 ans | Analyste Senior LCB-FT | Latence UI < 200ms, SHAP en langage naturel
**Dr. Ezra ARIS**, 29 ans | Data Scientist (PhD ML) | Parité Python/Java ε-près, CI/CD ONNX
**Axel**, 52 ans | Juriste & DPO | Logs WORM, Rapport d'audit, Model Cards
**Sophie**, 40 ans | Ingénieure SRE | Disponibilité 99.999%, Backpressure, JFR

</details>

---

## 3. Architecture Logicielle

Modélisation C4 complète du système, du niveau macro au niveau code. Chaque niveau hérite du
précédent par raffinement successif.

[`#00` 🌐 Vue Synoptique Globale — Pipeline ML](globalArchitectureDiagram/)  
Vue bout-en-bout du pipeline de détection : Flux ISO 20022 mTLS → Loom Orchestrator → Feature Engineering (Z-Score/Benford) → ONNX Inference (Bulkhead Anti-Pinning) → Neuro-Symbolic Bridge → décision blocage/WORM ou PostgreSQL pgvector + Redis.  
Inclut le cycle d'entraînement PyTorch → ONNX Export, le périmètre Zero Trust (Vault mTLS + K8s) et la boucle HITL SHAP/LIME. Diagramme transversal précédant la hiérarchie C4 formelle.

[`#02` 🏗️ C4 — Conteneurs (L2)]()  
Stack complète : HTTP Handler (VThreads), InferencePool (Platform Threads), Rule Engine, Feature Store, PostgreSQL + pgvector, Archive WORM, Prometheus/Grafana.

[`#03` 🔩 C4 — Composants (L3)]()  
Détail interne des conteneurs : `ONNXInferenceService`, `BulkheadExecutor`, `SHAPExplainer`, `WORMLogger`, `FallbackRuleEngine`, `VectorSearchService`.

[`#04` 💻 C4 — Code (L4)]()  
Classes critiques annotées : `ONNXSession`, `ThreadPoolExecutor` (dimensionnement CPU-bound), `WORMLogger` (hachage cryptographique), `SHAPExplainer` (top-3 features).

---

## 4. Modèle de Données

Définition du contrat de données : schéma relationnel, stratégie de Feature Store versionnée, et traçabilité complète de la chaîne de transformation (Data Lineage).

[`#13` 🗄️ ERD — PostgreSQL + pgvector]()  
Entités : `transactions`, `features_versioned`, `audit_logs_worm`, `onnx_models`, `embeddings` (pgvector).  
Clés étrangères, contraintes d'intégrité et indexation.

[`#16` 📦 Feature Store — Versionnement]()  
Définition unique et versionnée de chaque feature (ex. `avg_amount_7d`).  
Stratégies d'imputation documentées par champ (médiane / -1 / rejet). Aucune valeur `NaN` tolérée.

[`#14` 🔗 DFD — Data Lineage (Niveaux 0→2)]()  
Traçabilité complète : Source Core Banking → ETL → Feature Store versionné → Tenseur d'entrée ONNX. Conforme à l'exigence de reproductibilité absolue (§4.1).

---

## 5. Flux Métier & Traitements

Modélisation comportementale des traitements critiques, du cycle de vie d'une transaction à la déclaration de soupçon LCB-FT.

[`#05` ⚡ Séquence — Cycle de vie d'une transaction]()  
Flux nominal complet : Ingestion JSON → Validation (US-01) → Enrichissement <10ms (US-02) → Imputation (US-03) → Inférence ONNX (US-04) → Log WORM (US-10).

[`#06` 🧑‍⚖️ Séquence — Flux HITL (alerte critique)]()  
Architecture Human-in-the-Loop imposée par RGPD Art. 22 : alerte → interface analyste → SHAP top-3 (US-07) → décision Override (US-08) → log immuable.

[`#07` 🔄 Séquence — Fallback heuristique]()  
Latence ONNX > 500ms → Load Shedding → bascule automatique sur Rule Engine déterministe (US-05) → log de dégradation → reprise normale.

[`#08` 📋 BPMN — Processus LCB-FT / Déclaration de soupçon]()  
Processus complet Art. L561-15 CMF : alerte confirmée → investigation Nora → pré-rapport PDF (US-11) → validation DPO → transmission TRACFIN.

---

## 6. Concurrence JVM & Résilience

Modélisation des mécanismes de concurrence Java 21 (Project Loom), du problème critique de
**Pinning JNI** et du Pattern Bulkhead mis en œuvre comme stratégie de mitigation.

[`#10` 🧵 État — Virtual Thread (Pinning JNI)](virtualThreadAndPinningJNI/)  
Automate d'états d'un Virtual Thread : `Runnable` → `Parked` → **`Pinned`** (appel JNI ONNX) → `Unparked`. Événement JFR `jdk.VirtualThreadPinned` matérialisé.

[`#11` 🏗️ Séquence — Pattern Bulkhead]()  
Séparation Front-End (Virtual Threads, I/O Bound) vs Back-End Inférence (`ThreadPoolExecutor` Platform Threads, CPU/JNI Bound).  
Isolation stricte des ressources.

[`#12` 📉 Schéma Backpressure / Load Shedding]()  
File d'attente du pool d'inférence : seuil 500ms → rejet ou redirection des requêtes excédentaires.  
Métriques Micrometer/Prometheus intégrées.

[`#29` 🔁 Automate d'États — Résilience Système](#)  
États globaux du système : `Normal` → `Dégradé (Backpressure actif)` → `Fallback Rule Engine` → `Recovery` → `Normal`.  
Conditions de transition et timeouts.

<details>
<summary>⚠️ Criticité du Pinning JNI — Note d'architecture</summary>

Le Pinning survient lorsqu'un Virtual Thread effectue un appel JNI (natif) vers ONNX Runtime.  
Il monopolise alors son Carrier Thread (Platform Thread), annulant le bénéfice de la concurrence structurée de Loom.  
La stratégie Bulkhead (#11) est la **seule mitigation validée** ; toute tentative d'appel ONNX depuis un Virtual Thread constitue une régression critique détectable  par JFR.

</details>

---

## 7. Sécurité & Cyber-Résilience

Modélisation de la surface d'attaque spécifique aux systèmes d'IA financière (Evasion, Data Poisoning, Model Inversion) et des contre-mesures architecturales.

[`#19` ⚔️ Modèle de Menaces STRIDE]()  
Surfaces d'attaque : Evasion (Adversarial Examples), Data Poisoning (Feature Store), Model Inversion (rate limiting + arrondissement). Mapping menace → mitigation → composant.

[`#20` 🔑 Flux de Secrets — KMS / HashiCorp Vault]()  
Injection sécurisée des secrets au runtime : KMS/Vault → composants consommateurs.
**Zéro secret en dur dans le code source.** Rotation automatique documentée.

[`#21` 🛡️ Sanitization des Entrées (Anti-Adversarial)]()  
Flux de détection d'inputs atypiques (US-14) : input → détection d'anomalie statistique → score de confiance → rejet / quarantaine / log forensique.

---

## 8. MLOps & Cycle de Vie des Modèles

Modélisation du pipeline bout-en-bout de déploiement et de surveillance des modèles,
depuis l'entraînement Python jusqu'à la détection de dérive en production.

[`#09` 🔬 Activité — Cycle de vie d'un modèle ONNX]()  
Train Python (PyTorch/sklearn) → Export `.onnx` → Chiffrement au repos (US-13) → Tests de parité ε-près (US-06) → Blue/Green Deploy → Monitoring JFR.

[`#17` 🚀 Pipeline CI/CD MLOps]()  
Automatisation complète : commit → lint → unit tests → export ONNX → chiffrement → validation parité Java/Python (Golden Dataset) → déploiement conditionnel.

[`#18` 📊 Dérive de Modèle (Model Drift)]()  
Surveillance PSI / KL-Divergence sur la distribution des scores en production → alerte si déviation > 5% (US-15) → déclenchement automatique du pipeline de re-entraînement.

---

## 9. Conformité & Gouvernance

Documentation juridiquement opposable pour l'ACPR, la CNIL et les juridictions compétentes.
Chaque diagramme de cette section constitue une pièce du **bouclier juridique** du système.

[`#24` ⚖️ Matrice de Conformité Réglementaire](complianceMatrix/)  
Mapping exhaustif exigences ↔ User Stories (US-01 à US-15) : AI Act Annexe III, RGPD Art. 22, CMF L561-15, ISO/IEC 42001.  
Statut de conformité par exigence.

[`#22` 🔍 Processus FRIA — AI Act Art. 27]()  
Workflow d'évaluation d'impact sur les droits fondamentaux : identification → analyse → mitigation → **preuve de non-discrimination à l'instant T** intégrée dans le log.

[`#15` 🔒 DFD — Flux RGPD des données personnelles]()  
Cycle de vie RGPD : collecte → pseudonymisation → traitement algorithmique → exercice du droit d'accès (US-12) → purge conforme. Cartographie des transferts.

[`#23` 📼 Cycle de vie du Log WORM]()  
Génération → hachage cryptographique (chaîne de preuves) → écriture WORM immuable → requête d'audit → **rejeu déterministe** du modèle exact jusqu'à 5 ans après les faits.

<details>
<summary>📜 Exigence de déterminisme absolu — Contexte juridique</summary>

L'obligation de résultat imposée par l'Art. L561-15 CMF exige que la banque soit en mesure de « rejouer » toute transaction avec **le modèle exact de l'époque** et d'obtenir un résultat identique.  
Cette contrainte conditionne l'intégralité de l'architecture de Feature Store versionnée (#16), du log WORM (#23) et du pipeline CI/CD (#17).

</details>

---

## 10. Interface Utilisateur

L'interface n'est pas un accessoire ergonomique : elle est un **garant juridique**.  
Elle matérialise l'architecture HITL et doit permettre à l'analyste de renverser une décision algorithmique sur la base d'éléments explicables.

[`#27` 🖥️ Wireframe — Interface Analyste (Nora)]()  
Contraintes : latence d'affichage < 200ms, SHAP top-3 en langage naturel, bouton Override avec justification obligatoire, lien Data Lineage contextuel, génération rapport PDF L561-15.

---

## 11. Qualité & Tests

Plan de test draconien validant la mutation technologique Java 21, la parité des modèles et la couverture exhaustive de toutes les User Stories.

[`#28` 🏭 Déploiement — Infrastructure]()  
Conteneurs Docker/K8s, replicas HA, PostgreSQL avec réplication, stockage WORM dédié,
stack Prometheus + JFR + Grafana, ingress TLS. Dimensionnement cible.

[`#30` 🧪 Pyramide de Tests](testPyramid/)  
Niveaux : Unit → Intégration → Parité Python/Java (Golden Dataset) → Tests de Charge (5× charge nominale) → Audit Statique Pinning (`synchronized`) → Backtesting 12 mois.

[`#31` ✅ Matrice Traçabilité Tests ↔ User Stories](traceabilityMatrix/)  
Mapping exhaustif US-01 à US-15 ↔ cas de test, critères d'acceptation, persona validant et statut.  
Document de clôture juridique pour la validation ACPR.