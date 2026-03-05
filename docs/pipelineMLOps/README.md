# **Pipeline MLOps**<a href="../"><img src="../../assets/images/logo/linceul-audit-logo.webp" align="right" height="64"></a>
La Phase 3 (MLOps) constitue le système immunitaire de Linceul-Audit. Pour un projet de ce niveau doctoral, le monitoring ne peut se contenter d'alertes basiques ; il doit quantifier la phénoménologie de la dérive distributionnelle pour garantir la stationnarité des prédictions1.
---
## **1. Fondement Mathématique : L'Entropie Relative**
La divergence de Kullback-Leibler (KL) mesure la perte d'information lorsqu'on utilise une distribution $Q$ (données de production actuelles) pour approximer une distribution de référence $P$ (données d'entraînement).  
Pour des variables discrètes, elle se définit comme suit :
$$D_{KL}(P \parallel Q) = \sum_{x \in \mathcal{X}} P(x) \ln\left(\frac{P(x)}{Q(x)}\right)$$
Dans le cadre de Linceul-Audit, une valeur $D_{KL} > 0.1$ indique une dérive sémantique suspecte, tandis qu'une valeur $> 0.2$ impose un ré-entraînement immédiat du modèle pour rester conforme à l'IA Act.
## **Son architecture**
L'intégration de cette métrique s'inscrit dans un cycle de vie automatisé :  
1. Ingestion & Feature Engineering : Analyse des variables comme la vitesse transactionnelle ($\Delta t$) ou l'entropie du bénéficiaire.
2. Calcul de la Dérive : Chaque jour, le système compare les transactions du jour aux données du trimestre précédent via le script ci-dessus.
3. Model Registry (SHA-256) : Si une dérive est détectée, le nouveau modèle entraîné est versionné avec un hachage SHA-256 pour garantir l'intégrité exigée par l'ACPR8888.
4. Export ONNX : Le modèle validé est converti pour l'usine Java, éliminant toute latence de production.
## **Matrice de Monitoring des Variables**
Variable Scrutée|Impact de la Dérive|Seuil DKL​ Critique
---|---|--:
Vitesse transactionnelle|Évolution des comportements de botnets|0.15
Z-Score de montant|Inflation ou changement de profil client|0.10
Loi de Benford|Nouvelles méthodes de falsification comptable|0.05 (Stricte)
Patterns GNN|Restructuration des réseaux de blanchiment|0.20