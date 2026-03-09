# Code de Conduite<a href="https://github.com/MiKL5/linceul-audit"><img src="assets/images/logo/linceul-audit-logo.webp" align="right" height="64"></a>

> **Projet :** LINCEUL-AUDIT (LINCEUL-2026)  
> **Classification :** HAUTE CRITICITÉ — Système IA de détection de fraude financière  
> **Version du document :** 1.0.0  
> **Date d'entrée en vigueur :** 2026-03-09  
> **Basé sur :** [Contributor Covenant 3.0](https://www.contributor-covenant.org/version/3/0/code_of_conduct/)  
> **Mainteneur unique :** [@MiKL5](https://github.com/MiKL5/)

___

## Contexte et Portée

LINCEUL-AUDIT est un projet **individuel et indépendant**, développé et
maintenu par un seul auteur. Il n'existe pas de communauté de contributeurs
au sens large du terme.

Ce document ne gouverne donc pas des relations entre contributeurs, mais
définit :

1. Le **cadre d'interaction** attendu de toute personne interagissant avec  ce dépôt (issues, discussions, pull requests, commentaires) ;
2. L'**engagement éthique personnel** du mainteneur dans la conception et  le développement d'un système IA à haute criticité ;
3. Les **obligations réglementaires** auxquelles ce projet est soumis en tant que système IA à haut risque au sens de l'AI Act (Annexe III).

___

## Engagement du Mainteneur

En tant qu'auteur unique de ce projet, je m'engage à :

* Concevoir et développer ce système dans le **strict respect de la dignité humaine et des droits fondamentaux**, conformément à la Charte des droits fondamentaux de l'Union européenne ;
* Respecter les exigences de **transparence algorithmique** imposées par l'AI Act Art. 13 et Art. 14 — notamment par l'intégration de mécanismes d'explicabilité (SHAP, LIME) rendant les décisions du modèle intelligibles aux analystes humains ;
* Garantir l'**absence de biais discriminatoires** dans les données d'entraînement, les modèles et les règles métier, conformément à l'AI Act Art. 10 (gouvernance des données) et au RGPD Art. 22 (décision automatisée) ;
* Maintenir une **architecture Human-in-the-Loop (HITL) stricte** pour toute décision à effet juridique ou financier — aucune décision algorithmique finale n'est opposable sans validation humaine ;
* N'utiliser **aucune donnée personnelle réelle** dans le développement — exclusivement des données synthétiques conformes au RGPD Art. 5(1)(b) (limitation des finalités) et Art. 25 (privacy by design) ;
* Traiter avec **équité et réactivité** tout signalement de biais, de faille de sécurité ou de problème éthique soulevé par quiconque interagit avec ce dépôt ;
* Documenter **toutes les décisions architecturales significatives** (ADR) affectant l'équité, la transparence ou la sécurité du système ;
* Maintenir un **registre de traitement** conforme au RGPD Art. 30 pour toute donnée traitée par le système, même synthétique lors de la phase de développement.

___

## Comportements Attendus des Intervenants Externes

Toute personne interagissant avec ce dépôt — que ce soit via les issues,
les pull requests, les discussions ou tout autre canal — est invitée à :

* Formuler ses observations, rapports de bugs ou suggestions avec **précision et respect** ;
* **Citer les sources** de tout extrait de code, documentation ou donnée incorporé dans une contribution ou un commentaire ;
* Signaler tout **biais détecté, faille de sécurité ou problème éthique** de manière responsable, de préférence via le canal de divulgation responsable défini dans `SECURITY.md` ;
* Respecter que ce projet est développé **à titre personnel** dans un contexte académique — les délais de réponse peuvent varier en conséquence.

___

## Comportements Inacceptables

Les comportements suivants constituent des violations de ce Code de Conduite
et pourront entraîner la clôture immédiate de l'issue ou du commentaire
concerné, et le signalement à GitHub si nécessaire :

* **Harcèlement** sous toute forme — commentaires insultants, désobligeants, ou attaques personnelles ;
* **Violation de la confidentialité** — partage de données personnelles d'autrui sans consentement explicite (RGPD Art. 5(1)(f)) ;
* **Soumission de données réelles ou sensibles** dans les issues ou pull requests — toute contribution doit utiliser exclusivement des données synthétiques ou anonymisées ;
* **Introduction délibérée de biais** algorithmiques ou de backdoors dans les contributions proposées — infraction à l'AI Act Art. 10 et Art. 15 ;
* Tentative d'**usurpation ou d'appropriation** du code, de l'architecture ou de la documentation — protégés par le droit d'auteur français (CPI Art. L111-1) depuis la date du premier commit ;
* Tout contenu à caractère **discriminatoire, haineux ou illicite** au sens du droit français et européen.

___

## Signalement

Pour tout signalement relatif à ce Code de Conduite, à une faille éthique,
ou à un comportement inapproprié :

**Canal de contact :** *(renseigner ici une adresse e-mail dédiée)*

Pour les vulnérabilités de sécurité, utiliser exclusivement le canal défini
dans [`SECURITY.md`](SECURITY.md).

> En vertu du **RGPD Art. 5(1)(f)** et **Art. 32**, toute information
> personnelle communiquée dans le cadre d'un signalement sera traitée avec
> la plus stricte confidentialité, utilisée uniquement aux fins du traitement
> du cas, et supprimée dès sa résolution.

___

## Cadre Réglementaire de Référence

Ce Code de Conduite s'inscrit dans le cadre normatif suivant, par ordre
de primauté :

Référentiel | Disposition | Objet
---|---|---
AI Act (UE 2024/1689) | Art. 9, 10, 13, 14, 15 | Gestion des risques, données, transparence, supervision humaine, robustesse
RGPD (UE 2016/679) | Art. 5, 22, 25, 32 | Principes de traitement, décision automatisée, privacy by design, sécurité
CPI (France) | Art. L111-1, L121-1 | Droit d'auteur, droit moral
LCB-FT (France) | CMF Art. L561-1 ss | Lutte contre le blanchiment et le financement du terrorisme
Charte des DFU (UE) | Art. 8, 21, 47 | Protection des données, non-discrimination, recours effectif

___

## Révision

Ce document sera révisé en cas de :

* Évolution significative du cadre réglementaire applicable (AI Act, RGPD, LCB-FT) ;
* Modification du statut du projet (passage en mode collaboratif) ;
* Publication d'une nouvelle version majeure du Contributor Covenant.

> En cas de passage à un mode de développement collaboratif, ce document
> sera intégralement réécrit pour refléter les responsabilités mutuelles
> entre contributeurs, conformément au Contributor Covenant v3.0.

___

## Attribution

Ce Code de Conduite s'inspire du Contributor Covenant](https://www.contributor-covenant.org), **version 3.0**, adapté au contexte d'un projet solo à haute criticité réglementaire. Le Contributor Covenant est administré par l'Organization for Ethical Source et distribué sous licence [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).

___

<div align="center">

[← Retour README](README.md) · [Contribuer](CONTRIBUTING.md) · [Sécurité](SECURITY.md)

© 2026 — Projet Linceul Audit. Tous droits réservés.

</div>