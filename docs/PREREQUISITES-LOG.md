# **Journal des Prérequis Techniques**
> Historique des décisions de versionnement et de dépendances du projet Linceul-Audit.  
> Chaque entrée documente un changement réel, sa date, sa justification et son impact.  
> Toute entrée doit être ajoutée au moment du changement effectif — pas rétroactivement supposée.
---
## **Format d'une entrée**
* **Date**
* **Composant concerné**
* **Ancienne valeur → Nouvelle valeur**
* **Justification**
* **Impact / Risques**
---
## **Baseline actuelle (état de référence, non historisé)**
Ces prérequis constituent l'état courant documenté dans le README principal. Aucune version antérieure n'étant tracée à ce jour, ils sont consignés ici comme point de départ du présent journal.

Composant | Version de référence | Rôle dans l'architecture
---|---|---
Java (JDK) | 25 LTS | Runtime de l'Usine — Virtual Threads natifs, Foreign Function & Memory API (Panama)
Spring Boot | 3.x | Framework applicatif de l'Usine
Python | 3.13 | Runtime du Laboratoire — pré-traitement, entraînement des modèles
PostgreSQL | 18 | Base de données principale
pgvector | (extension PostgreSQL) | Recherche vectorielle de fraudes similaires
Redis | 8 | Cache et réduction de latence
ONNX Runtime | — | Moteur d'inférence, interfaçage Java ↔ modèles Python via API FFM
PyTorch | — | Entraînement des modèles Deep Learning côté Laboratoire
Scikit-learn | — | Modélisation Machine Learning classique côté Laboratoire
---
## **Historique des changements**
_(Aucun changement de version enregistré à ce jour depuis la création de ce journal. Les futures modifications de composants seront documentées ci-dessous, dans l'ordre chronologique.)_
### **[Date] — [Composant]**
- **Changement** : [ancienne version] → [nouvelle version]
- **Justification** :
- **Impact / Risques** :
---
## **Prérequis non stabilisés (à surveiller)**
Composant | Statut | Raison de l'instabilité
---|---|---
Moteur d'inférence GPU (CUDA) | Non tranché | Dépend du volume réel de transactions à traiter en production
Redis (mode cluster vs. standalone) | Non tranché | Décision différée jusqu'aux tests de charge sur `factory/`
Versions précises de Scikit-learn / PyTorch / ONNX Runtime | Non fixées | Le README ne spécifie pas encore de numéro de version exact — à préciser dès stabilisation du Laboratoire
---
## **Règle de mise à jour**
Toute modification de version majeure (langage, framework, base de données, extension) doit être consignée ici **avant** merge sur la branche principale, conformément au [CONTRIBUTING.md](../CONTRIBUTING.md).

<hr><div align="center">

[← Accueil](../README.md) · [Documentation](../docs/) · [Contribution](../CONTRIBUTING.md) · [Sécurité](../SECURITY.md)

___
© 2026 - Projet Linceul Audit. Tous droits réservés.