# 🔒 DFD : Flux RGPD des Données Personnelles<a href="../"><img src="../../../assets/images/logo/linceul-audit-logo.webp" align="right" height="64"></a>
<div align="center">

![RGPD](https://img.shields.io/badge/RGPD-Art.5_·_17_·_20_·_22-0055A4?style=flat-square&logo=eu&logoColor=white)
![LCB-FT](https://img.shields.io/badge/LCB--FT-L561--15_CMF-003189?style=flat-square)
![AI Act](https://img.shields.io/badge/AI_Act-Annexe_III-FF6D00?style=flat-square)
![WORM](https://img.shields.io/badge/Archive-WORM_5_ans-6D4C41?style=flat-square)
![Mermaid](https://img.shields.io/badge/Mermaid-DFD-FF3670?style=flat-square&logo=mermaid&logoColor=white)

</div>
___

## Table des Matières

1. [Contexte Juridique & Tensions Réglementaires](#1-contexte-juridique--tensions-réglementaires)
2. [Registre des Données Personnelles](#2-registre-des-données-personnelles)
3. [Niveau 0 — Vue Macro](#3-niveau-0--vue-macro)
4. [Niveau 1 — Flux Transactionnels](#4-niveau-1--flux-transactionnels)
5. [Niveau 1 — Flux Personas Internes](#5-niveau-1--flux-personas-internes)
6. [Niveau 2 — Cycle de Vie RGPD par Catégorie](#6-niveau-2--cycle-de-vie-rgpd-par-catégorie)
7. [Droits RGPD — Cartographie & Contraintes](#7-droits-rgpd--cartographie--contraintes)
8. [Transferts de Données & Destinataires](#8-transferts-de-données--destinataires)
9. [Diagrammes Liés](#9-diagrammes-liés)

___

## 1. Contexte Juridique & Tensions Réglementaires

Linceul Audit opère à l'intersection de deux obligations légales antagonistes :

Obligation | Base légale | Exigence | Impact RGPD
---|---|---|---
**Lutte contre le blanchiment** | CMF Art. L561-15 | Rétention des données **5 ans** minimum | Bloque le droit à l'effacement
**Protection des données** | RGPD Art. 17 | Effacement sur demande de la personne concernée | Limité par L561-15
**Décision automatisée** | RGPD Art. 22 | Droit à intervention humaine (HITL) | Architecture HITL obligatoire
**Transparence algorithmique** | AI Act Annexe III | Explicabilité SHAP opposable | Modèle ONNX + SHAP archivé
**Minimisation** | RGPD Art. 5(1)(c) | Seules les données strictement nécessaires | Features auditées par DPO Axel

> ⚖️ **Principe directeur** : en cas de conflit entre RGPD Art. 17 (effacement) et CMF L561-15 (rétention), la loi spéciale (L561-15) prime sur la loi générale (RGPD), **dans les limites strictes du traitement LCB-FT uniquement**.

___

## 2. Registre des Données Personnelles

### 2.1 Données Transactionnelles

Catégorie | Champ | Pseudonymisé | Rétention | Base légale
---|---|---|---|---
Identité émetteur | Nom, Prénom | ✅ Oui (hash SHA-256) | 5 ans | CMF L561-15
Coordonnées bancaires | IBAN émetteur / bénéficiaire | ✅ Oui (tokenisation) | 5 ans | CMF L561-15
Montant & devise | `amount`, `currency` | ❌ Non | 5 ans | CMF L561-15
Géolocalisation | Pays émetteur / bénéficiaire | ❌ Non | 5 ans | CMF L561-15
Horodatage | `timestamp` | ❌ Non | 5 ans | CMF L561-15
Score de risque | `onnx_score` | ❌ Non | 5 ans | CMF L561-15 + AI Act
Embeddings pgvector | Vecteur 768 dims | ✅ Oui (non réversible) | 5 ans | CMF L561-15

### 2.2 Données Personas Internes

Catégorie | Champ | Pseudonymisé | Rétention | Base légale
---|---|---|---|---
Identité analyste | Nom, ID, rôle (Nora) | ❌ Non (traçabilité juridique) | 5 ans | CMF L561-15
Actions HITL | Décision, justification, horodatage | ❌ Non | 5 ans | CMF L561-15
Accès DPO | Consultations Axel, validations | ❌ Non | 5 ans | CMF L561-15
Logs SRE | Accès technique Sophie | ✅ Oui | 1 an | RGPD Art. 5(1)(e)
Logs Data Scientist | Déploiements Ezra | ✅ Oui | 1 an | RGPD Art. 5(1)(e)

___

## 3. Niveau 0 — Vue Macro

```mermaid
flowchart TD
    PC["👤 Personne Concernée\n(Titulaire du compte)"]
    CB["🏦 Core Banking\n(Système source)"]
    LA["🔴 LINCEUL AUDIT\n(Responsable de traitement)"]
    TF["🏛️ TRACFIN\n(Autorité compétente)"]
    CNIL["🏛️ CNIL\n(Autorité de contrôle)"]
    ACPR["🏛️ ACPR\n(Superviseur bancaire)"]
    WORM["🗄️ Archive WORM\n(Stockage immuable 5 ans)"]

    CB -->|"Données transactionnelles\npseudonymisées"| LA
    LA -->|"Déclaration de soupçon\n(si fraude confirmée)"| TF
    LA -->|"Rapport d'audit\nAI Act / RGPD"| ACPR
    LA -->|"Registre traitements\nFRIA"| CNIL
    PC -->|"Exercice des droits\n(accès, rectification...)"| LA
    LA -->|"Réponse droits RGPD\n(sous 30 jours)"| PC
    LA -->|"Archivage immuable\nlog WORM + modèle ONNX"| WORM
```

___

## 4. Niveau 1 — Flux Transactionnels

```mermaid
flowchart TD
    CB["🏦 Core Banking\nISO 20022 mTLS"]

    subgraph LA["🔴 LINCEUL AUDIT — Périmètre de traitement"]
        ING["⚡ Ingestion\n& Validation"]
        PSEUDO["🔐 Pseudonymisation\nSHA-256 / Tokenisation"]
        FE["🔧 Feature Engineering\nZ-Score · Benford"]
        FS["📦 Feature Store\nversionné"]
        ONNX["🤖 Inférence ONNX\nv2.4.1"]
        SHAP["📊 SHAPExplainer\ntop-3 NLP"]
        HITL["🧑‍⚖️ Interface HITL\nNora — Décision"]
        WORM["🗄️ Log WORM\nimmuable"]
        PG["🗄️ PostgreSQL\n+ pgvector"]
    end

    TF["🏛️ TRACFIN"]
    PC["👤 Personne Concernée"]

    CB -->|"Transaction brute\nIBAN · montant · horodatage"| ING
    ING -->|"Données validées"| PSEUDO
    PSEUDO -->|"Données pseudonymisées\n(IBAN tokenisé, nom hashé)"| FE
    FE -->|"Features calculées"| FS
    FS -->|"Tenseur d'entrée"| ONNX
    ONNX -->|"Score + features actives"| SHAP
    SHAP -->|"Explication NLP top-3"| HITL
    HITL -->|"Décision + justification\nhorodatée"| WORM
    HITL -->|"Score + embedding"| PG
    HITL -->|"Déclaration de soupçon\n(si confirmée)"| TF
    PC -->|"Demande droit d'accès\n(US-12)"| HITL
    HITL -->|"Réponse pseudonymisée\nsous 30 jours"| PC
```

___

## 5. Niveau 1 — Flux Personas Internes

```mermaid
flowchart TD
    subgraph PERSONAS["👥 Personas Internes"]
        NORA["🧑‍⚖️ Nora\nAnalyste LCB-FT"]
        AXEL["👔 Axel\nDPO"]
        SOPHIE["🛠️ Sophie\nSRE"]
        EZRA["🔬 Ezra\nData Scientist"]
    end

    subgraph SYS["🔴 LINCEUL AUDIT"]
        WORM["🗄️ Log WORM\nimmuable"]
        AUDIT["📋 Rapport d'Audit"]
        ONNX_REG["📦 Registre Modèles\nONNX versionné"]
        ACCESS["🔐 Contrôle d'accès\nZero Trust / Vault"]
        LOGS_TECH["📜 Logs Techniques\npseudonymisés"]
    end

    CNIL["🏛️ CNIL"]
    ACPR["🏛️ ACPR"]

    NORA -->|"Décision HITL\njustification + ID analyste"| WORM
    NORA -->|"Consultation\naudit trail"| WORM
    AXEL -->|"Validation rapport\nsignature DPO"| AUDIT
    AXEL -->|"Registre traitements\nFRIA · Model Card"| CNIL
    AXEL -->|"Rapport conformité\nAI Act / RGPD"| ACPR
    SOPHIE -->|"Accès logs techniques\n(pseudonymisés)"| LOGS_TECH
    SOPHIE -->|"Supervision infra\nPrometheus / JFR"| LOGS_TECH
    EZRA -->|"Déploiement modèle\nONNX + hash SHA-256"| ONNX_REG
    EZRA -->|"Tests parité\nGolden Dataset"| ONNX_REG
    ACCESS -->|"Contrôle accès\nVault mTLS"| NORA
    ACCESS -->|"Contrôle accès\nVault mTLS"| AXEL
    ACCESS -->|"Contrôle accès\nVault mTLS"| SOPHIE
    ACCESS -->|"Contrôle accès\nVault mTLS"| EZRA
```

___

## 6. Niveau 2 — Cycle de Vie RGPD par Catégorie

### 6.1 Données Transactionnelles

```mermaid
flowchart LR
    A["📥 Collecte\nCore Banking\nISO 20022"] -->|"Validation\nschéma JSON"| B["🔐 Pseudonymisation\nSHA-256 · Tokenisation\n(à l'entrée du système)"]
    B -->|"Feature Engineering\nZ-Score · Benford"| C["⚙️ Traitement\nAlgorithmique\nONNX Inférence"]
    C -->|"Score ≥ seuil"| D["🧑‍⚖️ Intervention\nHumaine HITL\n(RGPD Art. 22)"]
    C -->|"Score < seuil"| E["🗄️ Archivage\nPostgreSQL\n+ pgvector"]
    D -->|"Confirmée"| F["📋 Déclaration\nde Soupçon\nTRACFIN"]
    D -->|"Classée SS suite"| E
    F --> G["🗄️ Archive WORM\n5 ans — immuable\n(CMF L561-15)"]
    E --> G
    G -->|"Délai 5 ans atteint"| H["🗑️ Purge\nconforme\nRGPD Art. 17"]

    style H fill:#b71c1c,color:#fff
    style G fill:#4e342e,color:#fff
    style D fill:#1565c0,color:#fff
```

### 6.2 Données Personas Internes

```mermaid
flowchart LR
    A1["🔐 Authentification\nVault mTLS\nZero Trust"] -->|"Session ouverte"| B1["📝 Action Métier\nou Technique\n(log généré)"]
    B1 -->|"Action HITL\nNora / Axel"| C1["🗄️ Log WORM\nimmuable\n5 ans — CMF L561-15"]
    B1 -->|"Accès technique\nSophie / Ezra"| D1["📜 Log Technique\npseudonymisé\n1 an — RGPD Art. 5"]
    C1 -->|"Délai 5 ans atteint"| E1["🗑️ Purge\nconforme"]
    D1 -->|"Délai 1 an atteint"| E1

    style E1 fill:#b71c1c,color:#fff
    style C1 fill:#4e342e,color:#fff
```

___

## 7. Droits RGPD — Cartographie & Contraintes

Droit RGPD | Article | Applicable | Contrainte LCB-FT | Procédure
---|---|---|---|---
**Droit d'accès** | Art. 15 | ✅ Oui | Données pseudonymisées uniquement | Requête → Axel (DPO) → réponse < 30 j (US-12)
**Droit de rectification** | Art. 16 | ⚠️ Limité | Impossible sur log WORM immuable | Annotation complémentaire autorisée
**Droit à l'effacement** | Art. 17 | ❌ Bloqué | CMF L561-15 impose 5 ans | Refus motivé par base légale
**Droit à la portabilité** | Art. 20 | ⚠️ Limité | Format machine (JSON) hors données LCB-FT | Export partiel autorisé
**Droit d'opposition** | Art. 21 | ❌ Bloqué | Obligation légale prime sur l'opposition | Refus motivé par CMF L561-15
**Droit à la limitation** | Art. 18 | ⚠️ Limité | Limitation possible hors périmètre LCB-FT | Gel du traitement non-LCB-FT
**Droit intervention humaine** | Art. 22 | ✅ Obligatoire | Architecture HITL imposée | Interface Nora — Override documenté
**Droit à l'explication** | AI Act Art. 27 | ✅ Obligatoire | SHAP top-3 en langage naturel | US-07 — Explicabilité ONNX

<details>
<summary>📜 Procédure d'exercice des droits RGPD — Détail opérationnel</summary>

1. La personne concernée soumet sa demande par écrit (email ou courrier) au DPO Axel.
2. Axel vérifie l'identité du demandeur (pièce d'identité).
3. Axel consulte le registre des traitements et identifie les données concernées.
4. Si les données relèvent du périmètre LCB-FT : réponse motivée de refus ou de limitation sous **30 jours**.
5. Si les données sont hors périmètre LCB-FT : traitement de la demande sous **30 jours** (extensible à 3 mois si complexité justifiée).
6. Toute réponse est archivée dans le log WORM avec horodatage et identité d'Axel.
7. En cas de contestation : saisine de la CNIL possible par la personne concernée.

</details>

___

## 8. Transferts de Données & Destinataires

Destinataire | Catégorie | Données transférées | Base légale | Garanties
---|---|---|---|---
**TRACFIN** | Autorité publique FR | Déclaration de soupçon (données pseudonymisées) | CMF L561-15 | Protocole sécurisé TRACFIN
**ACPR** | Autorité de contrôle FR | Rapports d'audit, Model Cards | CMF L511-41 | Canal sécurisé ACPR
**CNIL** | Autorité de contrôle FR | Registre traitements, FRIA | RGPD Art. 30 · AI Act Art. 27 | Canal sécurisé CNIL
**Core Banking** | Système interne | Score de risque (retour) | Contrat de traitement | mTLS interne

> 🌍 **Absence de transfert hors UE** : aucune donnée personnelle n'est transférée vers un pays tiers. Tous les systèmes opèrent dans le périmètre UE. Toute évolution architecturale introduisant un transfert hors UE **doit** faire l'objet d'une analyse d'impact (AIPD) et d'un mécanisme de transfert adéquat (RGPD Art. 46).

___

## 9. Diagrammes Liés

| # | Diagramme | Relation
---|---|---
`#14` 🔗 DFD — Data Lineage (Niveaux 0→2) | Traçabilité technique des features — complément au flux RGPD | Complémentaire
`#22` 🔍 Processus FRIA — AI Act Art. 27 | Évaluation d'impact dont ce DFD est une pièce maîtresse | Dépendant
`#08` 📋 BPMN — Processus LCB-FT | Processus métier encadrant la rétention 5 ans | Contextuel
`#23` 📼 Cycle de vie du Log WORM | Détail du stockage immuable référencé dans ce flux | Technique
`#13` 🗄️ ERD PostgreSQL + pgvector | Schéma de données sous-jacent aux flux niveau 2 | Technique
`#06` 🧑‍⚖️ Séquence — Flux HITL | Implémentation de RGPD Art. 22 matérialisée ici | Technique