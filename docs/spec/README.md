# CAHIER DES CHARGES TECHNIQUE ET FONCTIONNEL DE « LINCEUL AUDIT »<a href="../"><img src="../../assets/images/logo/linceul-audit-logo.webp" align="right" height="64"></a>
**Code Projet :** LINCEUL-2026  
**Classification :** CONFIDENTIEL / HAUTE CRITICITÉ  
**Architecture Cible :** Hybride Java 25 (Loom) / Python (Data Science) / ONNX  
**Conformité :** AI Act (UE), RGPD, CMF L561-15, ISO/IEC 42001
---
## **1. Introduction et Contexte Stratégique**
### **1.1. Préambule : Le Changement de Paradigme**
Le secteur financier européen fait face à une double rupture : l'industrialisation des vecteurs d'attaque (fraude synthétique, réseaux de mules, attaques adverses sur IA) et un durcissement sans précédent du cadre réglementaire. Le projet « Linceul Audit » ne vise pas une simple mise à niveau incrémentale des systèmes de surveillance des transactions (TMS). Il ambitionne de **redéfinir l'épistémologie de la détection de fraude** : passer d'une approche probabiliste opaque (« Boîte Noire ») à une approche déterministe, explicable et juridiquement opposable (« Boîte Grise »).  
L'objectif est de déployer une plateforme capable de traiter des flux massifs en temps réel avec une latence infra-milliseconde, tout en garantissant que chaque décision algorithmique soit auditable, équitable et conforme aux exigences de l'AI Act et de l'ACPR.
### **1.2. La Double Injonction : Performance vs Conformité**
Le système doit résoudre l'équation suivante :
1. **Performance (Throughput & Latency) :** Absorber les pics de charge (C10k problem) sans dégradation de service, en exploitant la concurrence structurée de Java 21 (Virtual Threads).
2. **Rigueur Juridique (Compliance) :** Garantir que l'utilisation de threads virtuels et de modèles neuronaux (Deep Learning) ne compromet ni la traçabilité ni la stabilité du système (problématique du *Pinning* JNI).
## **2. Cadre Normatif Avancé et Conformité (Compliance by Design)**
L'architecture est dictée par le droit. Chaque module logiciel doit répondre à une exigence légale précise.
### **2.1. AI Act et Gestion des Risques Fondamentaux**
* **Classification (Annexe III) :** Bien que bénéficiant de l'exception pour la "détection de la fraude financière", le système doit prouver qu'il n'est pas utilisé pour le *scoring* de crédit (Exigence REG-01).
* **FRIA (Fundamental Rights Impact Assessment) :** *Ajout critique.* Conformément à l'Art. 27 de l'AI Act, une étude d'impact sur les droits fondamentaux doit être intégrée. Le système doit logger non seulement la décision, mais aussi la preuve de non-discrimination à l'instant T.
### **2.2. RGPD Art. 22 et l'Architecture HITL**
L'interdiction de décision automatisée à effet juridique impose une architecture **Human-in-the-Loop (HITL)** stricte pour les alertes critiques. L'interface utilisateur n'est pas un accessoire, mais un **garant juridique**. Elle doit permettre à l'analyste de renverser la décision de la machine (Override) sur la base d'éléments explicables (SHAP Values).
### **2.3. LCB-FT et Traçabilité (Code Monétaire et Financier)**
L'obligation de résultat (Art. L561-15 CMF) impose une **journalisation WORM** (Write Once, Read Many). En cas d'enquête judiciaire 5 ans après les faits, la banque doit pouvoir "rejouer" la transaction avec le modèle exact de l'époque et obtenir le même résultat (Déterminisme absolu).
## **3. Architecture Technique : Haute Performance et Résilience**
### **3.1. Stack Technologique Cœur**
* **Orchestration & I/O :** Java 21 LTS avec **Project Loom (Virtual Threads)**.
* **Intelligence & Inférence :** Modèles entraînés en **Python**, exportés en **ONNX**, exécutés via **ONNX Runtime (Java API)**.
* **Données & Vecteurs :** PostgreSQL + **pgvector** (Recherche de similarité sémantique).
### **3.2. Gestion de la Concurrence et Mitigation du « Pinning »**
Le problème critique identifié est le *Pinning* (épinglage) des threads virtuels lors des appels natifs (JNI) vers ONNX, bloquant les threads porteurs (Carrier Threads).
**Stratégie de Mitigation (Pattern Bulkhead) :**
1. **Front-End (I/O Bound) :** Utilisation massive de Virtual Threads pour la gestion HTTP, la validation JSON et les appels BDD non bloquants.
2. **Back-End Inférence (CPU Bound / JNI) :** Isolation stricte de l'inférence dans un `ThreadPoolExecutor` dédié (Platform Threads), dimensionné selon le nombre de cœurs physiques.
3. **Surveillance JFR :** Activation permanente de *JDK Flight Recorder* avec l'événement `jdk.VirtualThreadPinned` pour détecter toute régression en production.
### **3.3. Résilience et Mode Dégradé (Fail-Safe)**
* **Backpressure (Load Shedding) :** Implémentation d'un mécanisme de délestage. Si la file d'attente du pool d'inférence dépasse un seuil critique (ex: 500ms de lag), les nouvelles requêtes sont rejetées ou redirigées.
* **Fallback Heuristique :** En cas d'indisponibilité du moteur ONNX ou de latence excessive, le système bascule automatiquement sur un moteur de règles "en dur" (Rule-based engine) déterministe pour assurer la continuité de service (Business Continuity Plan).
## **4. Ingénierie des Données et DataOps**
### **4.1. Feature Store et Lignage**
Pour garantir la reproductibilité :
* **Feature Store :** Centralisation des définitions de variables. Chaque feature (ex: "montant moyen sur 7 jours") doit avoir une définition unique et versionnée.
* **Data Lineage :** Capacité de tracer l'origine de chaque donnée (Source Core Banking -> Transformation ETL -> Tenseur d'entrée).
### **4.2. Qualité de Donnée (Data Quality Gate)**
* **Imputation Stratégique :** Aucun modèle ne doit recevoir de valeurs `NaN` ou `Null`. Une stratégie d'imputation stricte (médiane, -1, ou rejet) doit être définie et documentée pour chaque champ.
## **5. Personas Détaillés (Ingénierie Sociotechnique)**
L'adoption du système repose sur la satisfaction de quatre profils clés aux intérêts divergents.
### **Persona 1 : "L'Analyste Investigateur" (Utilisateur Final)**
* **Profil : Nora**, 34 ans, Analyste Senior LCB-FT (Lutte Contre le Blanchiment et le Financement du Terrorisme). Expert métier, sceptique vis-à-vis de l'IA "Magique". Travaille sous pression temporelle.
* Couleur : amber-500 (Clarté et Explicabilité).
* **Objectif** : Traiter 50+ alertes/jour sans erreur de jugement. Veut comprendre pourquoi une alerte est levée pour rédiger sa déclaration de soupçon (Tracfin).
* **Frustrations actuelles** : Trop de faux positifs, scores inexpliqués, devoir consulter 5 outils différents pour retracer l'origine d'une donnée.
* **Besoin Cognitif** : Réduction de la charge mentale. Elle a besoin d'une interface de Data Lineage (provenance) et d'une hiérarchisation de l'information via les facteurs SHAP (impact positif/négatif des variables sur le score).
* **Exigence Système** : Latence d'affichage < 200ms. Explicabilité en langage naturel et pré-remplissage des rapports d'enquête.
### Persona 2 : "L'Architecte Data Scientist" (Concepteur)
* **Profil :** **Dr. Ezra ARIS**, 29 ans, PhD en Machine Learning. Expert Python/PyTorch. Déteste les contraintes de production Java qui brident l'innovation algorithmique.
* Couleur : `cyan-500` (Performance et Fluidité).
* **Objectif** : Déployer ses modèles complexes (Auto-encodeurs, Random Forest, GNN) sans devoir les réécrire en Java.
* **Frustrations actuelles** : Le "Mur de la mise en prod". Ses modèles performants en labo deviennent lents ou bogués une fois recodés par les développeurs IT.
* **Besoin Technique** : Parité stricte entre Python et Java. Support total des opérateurs **ONNX Runtime** pour l'interopérabilité des modèles.
* **Exigence Système** : Pipeline de déploiement automatisé (CI/CD) qui valide que la fidélité de prédiction est maintenue à $10^{-6}$ près.
### Persona 3 : "Le Gardien de la Conformité" (DPO / Responsable Contrôle)
* **Profil** : **Axel**, 52 ans, Juriste et DPO. Aversion totale au risque réglementaire. Ne comprend pas le code, mais connaît le règlement (AI Act, RGPD) par cœur.
* Couleur : `emerald-500` (Éthique et Équité).
* **Objectif :** Prouver à l'ACPR et à la CNIL que la banque est en règle et que les modèles sont impartiaux.
* **Frustrations actuelles** : L'incapacité de l'IT à fournir des preuves d'audit claires sur une décision prise il y a 6 mois (Audit de Biais).
* **Besoin Fonctionnel** : Un bouton "Générer Rapport d'Audit" pour une transaction donnée, incluant la **Matrice de Confusion**, la version du modèle et la preuve de revue humaine.
* **Exigence Système** : Logs immuables (**WORM**), Documentation vivante (**Model Cards**) générée automatiquement.
### Persona 4 : "L'Ingénieur SRE" (Site Reliability Engineer)
* **Profil** : **Sophie**, 40 ans, Expert JVM. Obsédée par la disponibilité (99.999%) et la résilience du système face à la charge.
* Couleur Tailwind : `indigo-600` (Immuabilité et Stabilité).
* **Objectif** : Éviter que le serveur ne crashe sous la charge (*OutOfMemoryError*) ou ne se fige à cause du **Pinning des Virtual Threads**.
* **Frustrations actuelles** : Les modèles IA opaques qui consomment toute la RAM ou bloquent les threads lors des pics de transactions (Black Friday).
* **Besoin Technique** : Observabilité totale (Metrics Micrometer/Prometheus). Capacité à activer un **Load Shedding** (délestage) automatique si l'IA menace le Core Banking.
* **Exigence Système** : Isolation des ressources, Backpressure, Monitoring précis du temps d'exécution des modèles ONNX.
## 6. User Stories Exhaustives (Matrice Fonctionnelle)
### Épic 1 : Ingestion et Prétraitement (Data Engineering)
* **US-01 (Intégrité) :** En tant que Système, je dois valider le schéma JSON de chaque transaction entrante et rejeter immédiatement les formats invalides pour protéger le moteur d'inférence.
* **US-02 (Enrichissement) :** En tant que Système, je dois récupérer les données historiques du client (Feature Store) en moins de 10ms pour construire le vecteur de contexte.
* **US-03 (Imputation) :** En tant que Data Scientist, je veux que les valeurs manquantes soient remplacées selon la stratégie définie (ex: médiane) pour éviter les erreurs d'exécution du modèle ONNX.
### Épic 2 : Inférence et Détection (Core AI)
* **US-04 (Performance) :** En tant que SRE, je veux que l'inférence s'exécute dans un pool de threads dédié pour ne jamais bloquer les threads virtuels de gestion des requêtes (Anti-Pinning).
* **US-05 (Fail-Safe) :** En tant qu'Architecte, je veux que le système bascule automatiquement sur un mode "Règles simples" si le temps de réponse du modèle dépasse 500ms, afin de ne jamais bloquer une transaction client.
* **US-06 (Parité) :** En tant que Data Scientist, je veux qu'un test automatisé vérifie que le score calculé en Java est identique au score Python (delta < ) avant tout déploiement.
### Épic 3 : Interface Analyste et Décision (HITL)
* **US-07 (Explicabilité) :** En tant qu'Analyste (Camille), je veux voir les 3 principaux facteurs (SHAP) qui ont causé l'alerte, affichés en langage clair (ex: "Géolocalisation anormale"), pour prendre une décision rapide.
* **US-08 (Décision) :** En tant qu'Analyste, je veux pouvoir qualifier une alerte ("Faux Positif", "Fraude Confirmée") et ajouter un commentaire justifiant ma décision pour l'audit.
* **US-09 (Similitude) :** En tant qu'Analyste, je veux voir une liste de transactions passées similaires (via pgvector) pour identifier des schémas de fraude récurrents.
### Épic 4 : Audit et Conformité (Legal)
* **US-10 (Traçabilité) :** En tant que DPO (Hélène), je veux que chaque décision (auto ou humaine) génère un log cryptographique immuable contenant l'entrée, la version du modèle, et la sortie.
* **US-11 (Reporting Tracfin) :** En tant qu'Analyste, je veux générer un pré-rapport PDF conforme à l'article L561-15 CMF en un clic à partir d'une fraude confirmée.
* **US-12 (Droit d'accès) :** En tant que DPO, je veux pouvoir extraire l'historique complet des décisions algorithmiques concernant un client spécifique pour répondre à une demande d'accès RGPD.
### Épic 5 : Sécurité et MLOps
* **US-13 (Sécurité Modèle) :** En tant que RSSI, je veux que le fichier `.onnx` soit chiffré au repos et chargé en mémoire sécurisée pour empêcher le vol de propriété intellectuelle.
* **US-14 (Anti-Adversaire) :** En tant que Data Scientist, je veux que le système détecte et alerte sur les inputs atypiques (outliers extrêmes) qui pourraient signaler une tentative d'attaque par évasion ou empoisonnement.
* **US-15 (Dérive) :** En tant que Data Scientist, je veux recevoir une alerte automatique si la distribution des scores en production dévie de plus de 5% par rapport aux données de test (Data Drift).
## 7. Sécurité Avancée et Cyber-Résilience
### 7.1. Protection contre les Attaques Adversaires
L'IA est une surface d'attaque. Le système doit intégrer des défenses contre :
* **L'Evasion (Adversarial Examples) :** Perturbations mineures des données pour tromper le score. -> *Mitigation :* Entraînement robuste et sanitization des entrées.
* **L'Empoisonnement (Data Poisoning) :** Injection de données frauduleuses dans le set d'entraînement. -> *Mitigation :* Audit strict du Feature Store.
* **L'Inversion de Modèle :** Tentative de reconstruire les données d'entraînement (PII) à partir des sorties. -> *Mitigation :* Limitation du taux de requêtes (Rate Limiting) et sortie de scores arrondis/floutés pour les utilisateurs externes.
### 7.2. Gestion des Secrets
Aucun mot de passe ou clé de chiffrement dans le code. Utilisation obligatoire d'un **KMS (Key Management Service)** ou de **HashiCorp Vault** pour l'injection des secrets au runtime.
## 8. Stratégie de Test et Qualité (QA/QC)
Le plan de test doit être draconien pour valider le changement technologique.
1. **Tests de Charge (Load Testing) :** Simulation de 5x la charge nominale pour vérifier le comportement des Virtual Threads et l'absence de fuite mémoire (Heap Dump analysis).
2. **Tests de Parité (Inférence) :** Validation croisée systématique Python vs Java sur un dataset de référence (Golden Dataset).
3. **Audit de Code Statique :** Analyseur dédié pour détecter les blocs `synchronized` susceptibles de causer du Pinning.
4. **Backtesting :** Rejeu des 12 derniers mois de transactions sur le nouveau modèle avant bascule, pour mesurer l'impact financier (Faux Positifs / Faux Négatifs).
## 9. Conclusion et Validation
Ce cahier des charges définit un système **"State of the Art"**. Il ne se contente pas d'implémenter une technologie, il construit un **bouclier juridique et technique**. En intégrant l'auditabilité au cœur même de l'architecture (via le stockage des preuves et l'explicabilité), le projet « Linceul Audit » transforme la contrainte réglementaire en un atout de résilience opérationnelle.
**Validation Requise :**
* [ ] Direction Technique (Architecture)
* [ ] Direction Data (Modélisation)
* [ ] Direction Conformité (DPO/Légal)
* [ ] Direction Métier (Fraude)