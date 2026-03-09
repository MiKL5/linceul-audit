<h1 align="center"><b>LINCEUL-AUDIT</b><a href="https://github.com/mikl5/"><img src="assets/images/logo/linceul-audit-logo.webp" align="right" height="64"></a></h1>

**Linceul-Audit** est un écosystème d'ingénierie forensique neuro-symbolique industrialisé. Il est conçu pour l'audit exhaustif et la détection de fraudes financières complexes par la corrélation de flux ISO 20022 et de données non structurées. Cela garantit une explicabilité et une immuabilité conformes aux standards réglementaires internationaux.
---

<div align="center">

![Java](https://img.shields.io/badge/java-25%20LTS-orange?style=flat&logo=openjdk&logoColor=white)
![Project Loom](https://img.shields.io/badge/java-Project%20Loom%20(Virtual%20Threads)-red?style=flat&logo=openjdk&logoColor=white)
![Spring Boot](https://img.shields.io/badge/spring_boot-3.x-6DB33F?style=flat&logo=springboot&logoColor=white) 
![Python](https://img.shields.io/badge/python-3.13-blue?style=flat&logo=python&logoColor=FFD43B) 
![Scikit-learn](https://img.shields.io/badge/scikit--learn-Machine_Learning-F7931E?style=flat&logo=scikitlearn&logoColor=white) 
![PyTorch](https://img.shields.io/badge/pytorch-Deep%20Learning-EE4C2C?style=flat&logo=pytorch&logoColor=white) 
![ONNX Runtime](https://img.shields.io/badge/onnx-Runtime-005CED?style=flat&logo=onnx&logoColor=white) 
![PostgreSQL](https://img.shields.io/badge/postgresql-18.x-4169E1?style=flat&logo=postgresql&logoColor=white) 
![pgvector](https://img.shields.io/badge/extension-PG_Vector-0F9D58?style=flat&logo=postgresql&logoColor=white) 
![Redis](https://img.shields.io/badge/redis-8%20(cache%20%26%20latence)-DC382D?style=flat&logo=redis&logoColor=white) 
![Sécurité](https://img.shields.io/badge/sécurité-TLS%201.3%20%7C%20AES--256-232F3E?style=flat&logo=letsencrypt&logoColor=white)

### **Conformité Réglementaire**  
![AI Act](https://img.shields.io/badge/AI%20Act-UE%202024%2F1689-003399?style=flat&logo=europeancentralbank&logoColor=white) 
![RGPD](https://img.shields.io/badge/RGPD-Art.%2022-003399?style=flat&logo=europeancentralbank&logoColor=white) 
![CMF](https://img.shields.io/badge/CMF-L561--15-003399?style=flat) 
![ISO](https://img.shields.io/badge/ISO%2FIEC-42001-lightgrey?style=flat) 
![ACPR](https://img.shields.io/badge/ACPR-Conforme-green?style=flat) 
![CNIL](https://img.shields.io/badge/CNIL-Conforme-green?style=flat)

</div>

> ### **"Révéler l'invisible, protéger l'essentiel"**
> Linceul Audit est une plateforme de détection de fraude financière de 5ème génération.  
> Elle exploite l'architecture Java 25 (Panama/Loom) pour une inférence à latence nulle et assure une conformité totale avec le Règlement Européen sur l'IA (AI Act 2024).

<br><a href="#"><img src="assets/images/banners/banner.webp"></a>

## **⛔ Avertissement Légal (Propriété Intellectuelle)**
**CE PROJET EST PROPRIÉTAIRE.** Toute reproduction, copie, modification, distribution ou ingénierie inverse, partielle ou totale, de ce code source ou de ses concepts architecturaux est formellement interdite sans autorisation écrite explicite de l'auteur. 
Tout accès non autorisé fera l'objet de poursuites judiciaires immédiates.
<!-- ## **📑 Table des Matières**
1. [Architecture](#-architecture-&-stack-2026)
2. [Conformité réglementaire](#-conformité-réglementaire)
3. [Installation démarrage](#-installation-&-démarrage)
4. [Structure du projet](#-structure-du-projet)
5. [Fonctionnement du projet](#-fonctionnemnt-du-projet) -->
## **🏗 Architecture & Stack<!-- 2026-->**
Le projet repose sur une distinction stricte entre le Laboratoire (Recherche) et l'Usine (Production), utilisant les dernières versions LTS disponibles en 2026.
1. Le Laboratoire (Data Science) 
* 🧪 Langage : Python 3.13 (avec optimisation No-GIL activée pour le pré-traitement parallèle).
* Librairies : Scikit-learn, PyTorch 2.x, Polars (remplacement de Pandas pour les gros volumes).
* Output : Modèles exportés au standard ONNX Opset 21.
2. L'Usine (Production) 
* 🏭 Runtime : JDK 25.
  * Virtual Threads : Natifs et optimisés (plus de pinning sur les moniteurs synchronized depuis JDK 24).
  * Foreign Function & Memory API (Panama) : Utilisé pour l'interfaçage mémoire direct avec le moteur d'inférence, remplaçant JNI.
* Framework : Spring Boot 3.4+ (Support natif Java 25).
* Base de Données : PostgreSQL 18.1 avec extension pgvector v0.8+ pour la recherche vectorielle de fraudes similaires.
* Moteur d'Inférence : ONNX Runtime (Java API FFM variant).
## **⚖️ Conformité Réglementaire**
Le système est conçu pour naviguer dans le cadre légal strict de 2026.

Réglementation | Implémentation technique dans Linceul
---|---
AI Act (Annexe III) | Exception Fraude Financière : Le système est techniquement cloisonné pour ne servir que la détection de fraude, bénéficiant de l'exception prévue à l'Annexe III point 5(b) pour éviter la classification "Haut Risque" stricte du scoring de crédit.
RGPD Art. 22 | Human-in-the-Loop : Les décisions automatisées sont limitées aux scores faibles. Les scores élevés (>95) déclenchent une revue humaine obligatoire via l'interface Audit-UI.
CMF L561-15 | Tracfin Ready : Génération automatique des pré-déclarations de soupçon incluant les facteurs explicatifs (SHAP values) pour justifier le signalement.
ISO 42001 | AIMS Audit Trail : Journalisation cryptographique de chaque inférence (Input Hash + Model Version + Output) pour l'auditabilité post-mortem.
## **🚀 Installation & démarrage**

<details>
<summary><kbd>Lire</kbd></summary>

### **Prérequis**
Java 25 (Oracle)  
Python 3.13  
PostgreSQL 18.1
### **Procédure**
1. Démarrer l'infrastructure de données
2. Préparer le modèle (Python 3.13)
3. Compiler et Lancer l'Usine (Java 25)

</details>

## **⚡ Optimisations Java 25 (Loom & FFM)**
Contrairement aux architectures Java 21 (2023), Linceul Audit n'a plus besoin de pools de threads dédiés ("Bulkhead") pour éviter le pinning JNI, grâce aux avancées de Java 25.
**Intégration Panama (FFM)**
J'utilise l'API Foreign Function & Memory (stable) pour appeler le moteur ONNX. Cela permet de passer des pointeurs mémoire off-heap directement de Java à C++ sans copie coûteuse et sans bloquer les Virtual Threads.

## **📂 Structure du Projet**
```sh
linceul-audit/
│
├── assets/                                # Ressources visuelles
│   └── images/
│       ├── banners/
│       ├── infographics/
│       └── logo/
│
├── docs/                                   # Documentation
│   ├── architecture/
│   ├── complianceInfrastructure/
│   ├── decisionThreshold/
│   ├── diagrams/
│   ├── executiveSummary/
│   ├── mappingOfSystemicMalpractice/
│   ├── operation/
│   ├── pipelineMLOps/
│   ├── spec/
│   ├── systemArchitecture/
│   ├── taxonomyOfFraud/
│   ├── typeOfFraud/
│   └── README.md
│
├── data/                                  # Ignoré — géré par DVC
│   ├── raw/                               # Données brutes ISO 20022 / Core Banking
│   ├── processed/                         # Après nettoyage + imputation (US-03)
│   └── features/                          # Features engineerées — input modèle
│
├── tests/
│   └── fixtures/
│       └── golden_dataset/                # Git LFS — synthétique, anonymisé (US-06)
│
├── monitoring/
│   └── drift_baseline/                    # Git — JSON léger, statistiques de référence (US-15)
│
├── factory/                               # À venir - 🏭 L'Usine — Java 25 / Spring Boot 3.4+
│
├── laboratory/                            # À venir - 🧪 Le Laboratoire — Python 3.13 / Data Science
│
├── CONTRIBUTING.md
├── LICENSE
├── README.md
└── SECURITY.md
```

<hr><div align="center">

[Documentation](docs/) · [Contribuer](CONTRIBUTING.md) · [Sécurité](SECURITY.md) · [Code de Conduite](CODE_OF_CONDUCT.md)

© 2026 - Projet Linceul Audit. Tous droits réservés.
</div>