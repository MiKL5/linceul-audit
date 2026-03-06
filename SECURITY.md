# **SECURITY** <a href="./"><img src="assets/images/logo/linceul-audit-logo.webp" align="right" height="64"></a>
<div align="center">

[![Classification](https://img.shields.io/badge/Classification-CONFIDENTIEL%20%2F%20HAUTE%20CRITICITÉ-red?style=flat)](docs/spec)
[![RGPD](https://img.shields.io/badge/RGPD-Art.%2032%20%7C%2033-003399?style=flat&logo=europeancentralbank&logoColor=white)](docs/compliance)
[![AI Act](https://img.shields.io/badge/AI%20Act-Art.%209%20Risk%20Management-003399?style=flat)](docs/compliance)
[![ISO 27001](https://img.shields.io/badge/ISO-27001%20Aligned-lightgrey?style=flat)](docs/compliance)
[![Disclosure](https://img.shields.io/badge/Disclosure-Responsible-green?style=flat)](.)

</div>

> **Linceul Audit** est un système à **HAUTE CRITICITÉ** opérant sur des données financières sensibles.  
> La sécurité est structurellement intégrée à l'architecture — elle n'est pas une fonctionnalité optionnelle.  
> Ce document définit la politique de sécurité, la procédure de divulgation responsable et les engagements de réponse.

---

## Versions supportées

Version|Supportée|Notes
---|---|---
`master` (production)|✅ Patches prioritaires sous 7 jours|Branche protégée — production certifiée
`develop` (HEAD)|✅ Activement maintenue|Intégration continue
`release/*` en cours|✅ Gel du code — patches appliqués|Phase de stabilisation
Versions `< v1.0.0`|⚠️ Best-effort uniquement|Aucune garantie de patch

## Signalement d'une vulnérabilité

### ⚠️ Ne jamais créer d'issue publique GitHub

Pour toute vulnérabilité de sécurité, **n'ouvrez pas d'issue publique**. Une divulgation prématurée pourrait :
* Exposer des données financières sensibles de clients
* Révéler les mécanismes de détection de fraude à des acteurs malveillants
* Constituer une violation du RGPD Art. 32 et de l'obligation de confidentialité CMF

### Canal de signalement officiel

**Option 1 — GitHub Security Advisories (recommandé) :**  
`Security` → `Advisories` → `Report a vulnerability` sur ce dépôt

**Option 2 — Contact direct (vulnérabilités critiques) :**

Rôle|Contact
---|---
Responsable Sécurité (RSSI)|`[À COMPLÉTER]`
DPO — Données Personnelles|`[À COMPLÉTER]`

### Informations à fournir dans le rapport

```
Titre          : Description concise
Criticité      : Critique / Haute / Moyenne / Faible  (CVSS v3.1 si possible)
Composant      : ingestion · inference · hitl · audit · security · mlops
Environnement  : Version du système, OS, configuration
Reproduction   : Étapes détaillées et reproductibles
Impact         : Données potentiellement exposées, vecteur d'attaque, périmètre
Preuve (PoC)   : Code ou capture d'écran (joint en pièce chiffrée pour les critiques)
```

> Pour les vulnérabilités **Critiques**, chiffrer le rapport avec la clé GPG publique du projet `[À PUBLIER]` avant envoi.

## Délais de réponse engagés

Criticité|Accusé de réception|Analyse initiale|Patch déployé|Divulgation publique
---|---|---|---|---
🔴 Critique (CVSS ≥ 9.0)|24 h|48 h|7 jours|Après patch + 7 jours
🟠 Haute (CVSS 7.0–8.9)|48 h|5 jours|30 jours|Après patch + 14 jours
🟡 Moyenne (CVSS 4.0–6.9)|72 h|7 jours|60 jours|Après patch + 30 jours
🟢 Faible (CVSS < 4.0)|1 semaine|14 jours|90 jours|Discrétionnaire


## Périmètre de sécurité

### ✅ In Scope — Vulnérabilités prioritaires

**Critiques**
* Injection ou manipulation du pipeline d'inférence (contournement de la détection de fraude)
* Exfiltration de données financières ou personnelles (RGPD Art. 32)
* Contournement du mécanisme HITL obligatoire (violation RGPD Art. 22)
* Accès non autorisé ou modification des logs WORM (violation CMF L561-15)
* Compromission du Feature Store (attaque par Data Poisoning — CDC §7.1)

**Hautes**
* Attaques adversariales sur le modèle ONNX : Évasion, Inversion de modèle (US-14)
* Fuite ou compromission de la clé de chiffrement du fichier `.onnx` (US-13)
* Contournement du rate limiting exposant à l'inversion de modèle
* Vulnérabilités dans l'API Panama/FFM pouvant induire une corruption mémoire

**Moyennes**
* Attaques sur la chaîne d'approvisionnement (supply chain — dépendances empoisonnées)
* Mauvaise configuration des Virtual Threads induisant un Pinning non détecté
* Dérive du modèle non détectée permettant de contourner la détection (US-15)

### ❌ Out of Scope

* Attaques nécessitant un accès physique non autorisé à la machine
* Ingénierie sociale ciblant les contributeurs du projet
* Vulnérabilités dans les dépendances tierces sans patch disponible de leur mainteneur
* Tests de charge / DoS sur les environnements de développement ou de staging

## Architecture de sécurité en place

<details>
<summary>🔒 Mesures techniques implémentées (CDC §7)</summary>

Couche|Mesure|Référence
---|---|---
**Secrets**|KMS / HashiCorp Vault — zéro secret dans le code|CDC §7.2
**Modèle**|Artefacts `.onnx` chiffrés AES-256 au repos|US-13
**Transport**|TLS 1.3 obligatoire sur tous les canaux réseau|CDC §3.1
**Logs**|Journalisation WORM cryptographique immuable|CMF L561-15
**Inputs**|Sanitization et validation avant inférence|US-14
**Monitoring**|JFR `jdk.VirtualThreadPinned` + Prometheus/Micrometer|CDC §3.2
**Accès données**|Principe du moindre privilège sur PostgreSQL|RGPD Art. 32
**Endpoints**|Rate Limiting sur les endpoints d'inférence|CDC §7.1
**Entraînement**|Audit strict du Feature Store (anti-Data Poisoning)|CDC §7.1
**Scores**|Arrondissement des scores exposés (anti-inversion)|CDC §7.1

</details>

---

## Politique de divulgation publique

Une fois le patch déployé sur `master`, la procédure de divulgation est la suivante :

1. **GitHub Security Advisory** public créé avec CVE associé (si applicable)
2. **`CHANGELOG.md`** mis à jour avec une section `[Security]` détaillant la correction
3. **DPO notifié** si des données personnelles ont pu être exposées — notification CNIL obligatoire sous **72 h** (RGPD Art. 33)
4. **ACPR notifiée** si l'intégrité du système de détection de fraude a été compromise (CMF L561-15)

<!-- ___

## Remerciements

Les chercheurs en sécurité ayant contribué responsablement à améliorer la posture de sécurité du projet seront mentionnés ici avec leur accord explicite. -->

___

<div align="center">

[← README](README.md) · [Documentation](docs/) · [Contribuer](CONTRIBUTING.md)

___
© 2026 - Projet Linceul Audit. Tous droits réservés.
</div>
