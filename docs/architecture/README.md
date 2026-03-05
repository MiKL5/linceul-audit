# **🏗️ ARCHITECTURE SYSTÉMIQUE**<a href="../"><img src="../../assets/images/logo/linceul-audit-logo.webp" align="right" height="64"></a>
Cette architecture "N-Tier" est segmentée en deux domaines majeurs ➜ **_le Laboratoire (Plan de Contrôle)_** et **_l'Usine (Plan d'Exécution)_**, reliés par le pivot d'interopérabilité ONNX.
---
## **1. Plan d'Exécution (Java 25 Factory)**
C'est le cœur réactif du système, orchestré par la JVM pour une disponibilité critique.
* **La couche d'Ingestion (Loom Orchestrator)** :
   * Réception des flux ISO 20022 et documents non structurés via des terminaux sécurisés mTLS.
   * Utilisation intensive des Virtual Threads (Project Loom) pour gérer des milliers de connexions concurrentes sans saturation mémoire.
* **Le moteur d'Inférence (Native Shield)** :
   * Intégration du runtime ONNX via JNI.
   * Mécanisme Anti-Pinning ➜ Implémentation du pattern Bulkhead via un pool de threads plateformes dédié pour isoler les appels natifs et protéger l'ordonnanceur Loom.
* **Couche de Raisonnement (Neuro-Symbolic Bridge)** :
   * Combinaison des scores de l'IA avec des règles métier déterministes (Loi de Benford, seuils réglementaires).
## **2. Plan de Données et Persistance**
L'intégrité de la preuve est garantie par une hybridation de technologies SQL et vectorielles.
* **Intelligence Sémantique** ➜ PostgreSQL 18.1 avec l'extension pgvector 8.1 pour le stockage et la recherche de similarité sur les embeddings de transactions.
* **Cache de Vélocité** ➜ Redis pour la détection de patterns en temps réel (fraude à la rapidité).
* **Registre de Preuve Immuable** ➜ Stockage de type WORM (Write Once Read Many) sécurisé par un hachage en chaîne (Chain-hashing SHA-256), assurant une traçabilité inviolable conforme à la norme NF Z42-013.
## **3. Plan de Contrôle (Python 3.13 Lab & MLOps)**
C'est ici que l'intelligence est forgée et surveillée.
* **Entraînement Forensique** ➜ Utilisation de Scikit-learn et PyTorch pour les modèles Isolation Forest, Auto-encodeurs et GNN.
* **Pipeline MLOps** ➜ * Export automatique vers le format ONNX.
   * **Drift Monitoring** ➜ Surveillance continue de la dérive sémantique via les tests de Kolmogorov-Smirnov et la divergence KL.
## **4. Couche de Sécurité et Gouvernance (Zero Trust)**
La sécurité est une exigence absolue intégrée à chaque strate.
* **Gestion des Secrets** ➜ Utilisation de HashiCorp Vault pour le chiffrement des modèles ONNX et la rotation des certificats mTLS.
* **Interface HITL (Human-in-the-Loop)** ➜ Tableau de bord analyste intégrant l'explicabilité SHAP/LIME pour valider les alertes "Orange" (80-95%).
* **Orchestration** ➜ Déploiement sous Kubernetes avec isolation stricte des conteneurs et quotas de ressources.
___
## **Synthèse des Flux**
1. **Ingestion** ➜ Un flux ISO 20022 entre dans l'usine Java.
2. **Vecteur** ➜ Le Feature Engineering (Z-Score, Benford) crée le vecteur d'entrée.
3. **Inférence** ➜ Le pool de workers natifs exécute le modèle ONNX sans bloquer Loom.
4. **Décision** ➜ Si le score est > 95% (Zone Rouge), blocage immédiat et signature du log immuable.
5. **Audit** ➜ L'analyste humain révise les cas ambigus via SHAP et alimente la boucle de feedback MLOps.