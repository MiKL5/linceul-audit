# **Le seuil décisionnel**<a href="../"><img src="../../assets/images/logo/linceul-audit-logo.webp" align="right" height="64"></a>
La gestion des seuils est le pivot de la conformité au RGPD (Art. 22) et à l'IA Act.  
Elle ne repose pas uniquement sur un score de probabilité, mais aussi sur une matrice de risque corrélant confiance statistique et impact juridique.

Zone|Score de Risque|Action Système|Justification Ingénierie & Droit
:-:|:-:|---|---
Verte|0% - 80%|Validation Automatique|Risque de faux négatifs jugé acceptable pour la fluidité des flux ISO 20022. Les transactions sont enregistrées dans le registre immuable pour audit a posteriori.
Orange|80% à 94%|Revue Humaine (HITL)|Exigence RGPD : Toute décision ayant un effet juridique doit pouvoir être révisée par un humain. L'IA fournit ici une visualisation SHAP pour expliquer le score à l'analyste.
Rouge|5% à 100%|Blocage & Alerte Tracfin|IA Act : Système à haut risque avec blocage préventif. Génération automatique d'un rapport de preuve haché en chaîne pour garantir l'intégrité de la détection en cas de litige.