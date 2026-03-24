# Diagrammesa<a href="../"><img src="../../assets/images/logo/linceul-audit-logo.webp" align="right" height="64"></a>

## Table des Matières

1. [Contexte & Vision](#1-contexte--vision)
2. [Architecture Système](#2-architecture-système)
3. [Pipeline ML & Dictionary Learning](#3-pipeline-ml--dictionary-learning)
4. [Conformité Réglementaire](#4-conformité-réglementaire)
5. [Données & DataOps](#5-données--dataops)
6. [Sécurité & Cyber-Résilience](#6-sécurité--cyber-résilience)
7. [Interfaces & Personas](#7-interfaces--personas)
8. [Qualité & Tests](#8-qualité--tests)

---

## 1. Contexte & Vision

Linceul Audit opère un changement de paradigme épistémologique : passer d'une détection de fraude
probabiliste opaque (« Boîte Noire ») à une approche déterministe, explicable et
**juridiquement opposable** (« Boîte Grise / Blanche »), articulée autour de 20 diagrammes répartis dans 6 domaines.

<details>
<summary>ℹ️ Légende des symboles utilisés dans tous les diagrammes C4</summary>

* 🟦 **Système principal** — Linceul Audit
* 🟩 **Acteur humain** — Personas internes
* 🟥 **Système externe** — Core Banking, ACPR, CNIL, TRACFIN
* ⬛ **Stockage** — PostgreSQL, WORM Archive

</details>

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

## 2. Architecture Système

Modélisation C4 complète du système couvrant la vue macro (contexte), les conteneurs, les composants et le code. Chaque niveau hérite du précédent par raffinement successif. Le pipeline nominal est : **Transaction → scoring → décision → log WORM**.

[`D-01` 🌐 Vue macro de la plateforme](globalArchitectureDiagram/)  
Vue bout-en-bout : Transaction → scoring → décision → log WORM.  
Flux ISO 20022 mTLS → Loom Orchestrator → Feature Engineering (Z-Score/Benford) → ONNX Inference → Neuro-Symbolic Bridge → PostgreSQL pgvector + Redis.  
Inclut le périmètre Zero Trust (Vault mTLS + K8s) et la boucle HITL SHAP/LIME.

[`D-02` 🏗️ Pattern Bulkhead](bulkhead/)  
Séparation architecturale en trois zones : **Zone I/O** (Virtual Threads, I/O Bound) · **Zone Inférence** (`ThreadPoolExecutor` Platform Threads, CPU/JNI Bound) · **Zone Fallback** (Rule Engine déterministe).  
Isolation stricte des ressources contre le Pinning JNI.

[`D-03` 🚀 Infrastructure de déploiement](deploymentInfrastructure/)  
Stack complète : Spring Boot · Redis · PostgreSQL + pgvector · HashiCorp Vault · ONNX Runtime.  
Conteneurs Docker/K8s, replicas HA, stockage WORM dédié, stack Prometheus + JFR + Grafana, ingress TLS.

[`D-04` ⚡ Séquence de traitement](transactionLifecycle/)  
Flux nominal complet : Ingestion JSON → Validation (US-01) → Enrichissement <10ms (US-02) → Imputation (US-03) → Inférence ONNX (US-04) → Log WORM (US-10).  
Chronologie milliseconde.

<details>
<summary>⚠️ Criticité du Pinning JNI — Note d'architecture</summary>

Le Pinning survient lorsqu'un Virtual Thread effectue un appel JNI (natif) vers ONNX Runtime.  
Il monopolise alors son Carrier Thread (Platform Thread), annulant le bénéfice de la concurrence structurée de Loom.  
La stratégie Bulkhead (`D-02`) est la **seule mitigation validée** ; toute tentative d'appel ONNX depuis un Virtual Thread constitue une régression critique détectable par JFR (`jdk.VirtualThreadPinned`).

</details>

---

## 3. Pipeline ML & Dictionary Learning

Modélisation complète du pipeline d'apprentissage automatique reposant sur le **Dictionary Learning (ISTA)** comme couche de transparence « Boîte Blanche », combinée à des modèles ensemblistes et un AutoEncoder. Le pipeline est bout-en-bout exportable en ONNX statique.

[`D-05` 🔬 Pipeline ONNX fusionné](onnxPipeline/)  
StandardScaler → ISTA(T=10) → Random Forest / XGBoost + AutoEncoder.  
Export `.onnx` unique chiffré au repos (US-13).

[`D-06` 📐 DL : entraînement vs inférence](dictionaryLearning/)  
Entraînement Python MiniBatchDictionaryLearning → dictionnaire `D` figé → ISTA déroulé en ONNX statique.  
Séparation stricte des phases train / infer.

[`D-07` 🔁 ISTA T=10 déroulé (ONNX statique)](istaUnrolled/)  
10 itérations de soft-thresholding unrollées en graphe ONNX statique.  
Garantit la reproductibilité absolue et l'absence de boucle dynamique à l'inférence.

[`D-08` 🪟 Transparence Boîte Blanche / Grise](explainability/)  
Couche DL → **Boîte Blanche** (coefficients sparses interprétables) · RF/AE → **Boîte Grise** (SHAP top-3) · Score de fusion final.  
Conforme AI Act Annexe III exigences d'explicabilité.

[`D-09` 🔄 Cycle de vie modèle (MLOps)](mlopsLifecycle/)  
Train Python (PyTorch/sklearn) → Export `.onnx` → Chiffrement → Tests de parité ε-près (US-06) → Blue/Green Deploy → Monitoring JFR.  
Pipeline CI/CD automatisé : commit → lint → unit tests → export ONNX → validation parité Java/Python (Golden Dataset) → déploiement conditionnel.

---

## 4. Conformité Réglementaire

Documentation juridiquement opposable pour l'ACPR, la CNIL et les juridictions compétentes.
Chaque diagramme de cette section constitue une pièce du **bouclier juridique** du système.
Périmètre réglementaire : **AI Act · RGPD · CMF · ISO 42001**.

[`D-10` ⚖️ Mapping réglementaire](complianceMatrix/)  
Mapping exhaustif exigences ↔ User Stories (US-01 à US-15) : AI Act Annexe III, RGPD Art. 22, CMF L561-15, ISO/IEC 42001.  
Statut de conformité par exigence.

[`D-11` 🧑‍⚖️ Workflow HITL (score ≥ 0,95)](hitlWorkflow/)  
Architecture Human-in-the-Loop imposée par RGPD Art. 22 : alerte critique (score ≥ 0,95) → interface analyste → SHAP top-3 (US-07) → décision Override (US-08) → log immuable.

[`D-12` 📼 Log WORM & audit trail](wormLog/)  
Génération → hachage SHA-256 (hash tenseur inclus) → écriture WORM immuable → requête d'audit.  
Chaîne de preuves cryptographiquement vérifiable.

[`D-13` 🔁 Replay judiciaire déterministe](judicialReplay/)  
Modèle figé + dictionnaire `D` figé + T fixe → résultat ≡ à l'identique.  
**Rejeu déterministe** jusqu'à 5 ans après les faits, conforme Art. L561-15 CMF.

<details>
<summary>📜 Exigence de déterminisme absolu — Contexte juridique</summary>

L'obligation de résultat imposée par l'Art. L561-15 CMF exige que la banque soit en mesure de « rejouer » toute transaction avec **le modèle exact de l'époque** et d'obtenir un résultat identique.  
Cette contrainte conditionne l'intégralité de l'architecture de Feature Store versionnée (`D-15`), du log WORM (`D-12`) et du pipeline CI/CD (`D-09`).

</details>

---

## 5. Données & DataOps

Définition du contrat de données : schéma relationnel, stratégie de Feature Store versionnée, traçabilité complète (Data Lineage) et surveillance de la dérive des distributions.

[`D-14` 🔗 Data Lineage](dataLineage/)  
Traçabilité complète : Source Core Banking → ETL → Feature Store versionné → Tenseur d'entrée ONNX.  
Conforme à l'exigence de reproductibilité absolue (§4.1).

[`D-15` 📦 Feature Store versionné](featureStore/)  
Définition unique et versionnée de chaque feature : `D_version` · `scaler` · `T` · `λ` · `seuil_anomalie`.  
Stratégies d'imputation documentées par champ (médiane / -1 / rejet). Aucune valeur `NaN` tolérée.

[`D-16` 📊 Drift monitoring](driftMonitoring/)  
Surveillance de l'erreur de reconstruction en sliding window.  
Alerte si déviation > 5% (US-15) → déclenchement automatique du pipeline de re-entraînement.

---

## 6. Sécurité & Cyber-Résilience

Modélisation de la surface d'attaque spécifique aux systèmes d'IA financière (Évasion, Data Poisoning, Model Inversion) et des contre-mesures architecturales Zero Trust.

[`D-17` ⚔️ Threat model](threatModel/)  
Surfaces d'attaque : Évasion (Adversarial Examples) · Empoisonnement (Feature Store) · Inversion (rate limiting + arrondissement).  
Mapping menace → mitigation → composant.

[`D-18` 🛡️ Résilience & mode dégradé](resilienceMode/)  
Backpressure · Load Shedding · Fallback ISTA (Rule Engine déterministe).  
États globaux : `Normal` → `Dégradé (Backpressure actif)` → `Fallback Rule Engine` → `Recovery` → `Normal`.  
Métriques Micrometer/Prometheus intégrées.

<details>
<summary>🔑 Gestion des secrets — KMS / HashiCorp Vault</summary>

Injection sécurisée des secrets au runtime : KMS/Vault → composants consommateurs.  
**Zéro secret en dur dans le code source.** Rotation automatique documentée.

</details>

---

## 7. Interfaces & Personas

L'interface n'est pas un accessoire ergonomique : elle est un **garant juridique**.
Elle matérialise l'architecture HITL et doit permettre à l'analyste de renverser une décision algorithmique sur la base d'éléments explicables.

[`D-19` 👥 Carte des personas](personaMap/)  
Interactions des 4 personas : Nora · Ezra · Axel · Sophie → systèmes et flux correspondants.  
Matrice des responsabilités fonctionnelles (Use Cases UML).

[`D-20` 🖥️ Interface analyste — Wireframe (Nora)](wireframeNora/)  
Contraintes : latence d'affichage < 200ms, SHAP top-3 en langage naturel, atomes Dictionary Learning interprétables.  
Bouton Override avec justification obligatoire, lien Data Lineage contextuel, génération rapport PDF L561-15.

___
[← README](../../) · [Contribuer](../../CONTRIBUTING.md) · [Sécurité](../../SECURITY.md) · [Code de Conduite](../../CODE_OF_CONDUCT.md)