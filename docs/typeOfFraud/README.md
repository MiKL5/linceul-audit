# **Les types de fraude**<img src="../../assets/images/logo.png" align="right" height="64"></a>
Le système est conçu pour identifier une taxonomie étendue de malversations, allant des schémas de blanchiment sophistiqués aux manipulations comptables discrètes.  
Sa force réside dans sa capacité à corréler des signaux faibles via une approche neuro-symbolique.
---
## **1. Criminalité Financière et Blanchiment (AML)**
Le système excelle dans la détection des structures de dissimulation de capitaux, souvent invisibles lors d'un audit manuel.  
* **_Le Smurfing (Schtroumpfage)_** : Détection de la fragmentation de sommes importantes en une multitude de petits dépôts pour contourner les seuils de déclaration réglementaires.
* **_Le Structuring (Structuration)_** : Identification de schémas où les fonds sont placés et déplacés de manière complexe pour masquer leur origine illicite.
* **_Le Carrousel TVA_** : Repérage de réseaux de sociétés éphémères réalisant des transactions transfrontalières fictives pour récupérer indûment la TVA.
* **_Le Wash Trading_** : Identification de transactions circulaires ($A \rightarrow B \rightarrow C \rightarrow A$) n'ayant aucune substance économique, détectées grâce aux algorithmes de graphes (GNN).
## **2. Fraude Comptable et Altération de Bilans**
Grâce au moteur statistique, le système agit comme un "scanner de vérité" sur les journaux comptables.
* **_Inventions de Chiffres (Falsification)_** : Utilisation de la Loi de Benford pour détecter les anomalies dans la distribution des premiers chiffres des montants, révélant des données saisies manuellement de manière arbitraire.
* **_Anomalies de Montants_** : Calcul du $Z$-score pour isoler les écritures comptables présentant un écart-type critique par rapport à l'historique de l'entité ou du secteur.
* **_Entropie des Bénéficiaires_** : Analyse de la dispersion inhabituelle des paiements vers des tiers non référencés ou présentant des caractéristiques de risque (paradis fiscaux, listes de sanctions).
## **3. Cyber-Fraude et Ingénierie Sociale**
Le système intègre des métriques comportementales pour détecter les attaques technologiques.
* **_Fraude au Président (Social Engineering)_** : Détection de virements urgents et atypiques déclenchés par usurpation d'identité de dirigeants.
* **_Détection d'Automates (Bots)_** : Analyse de la vitesse de frappe et de la latence de validation lors de la saisie des transactions pour distinguer un opérateur humain d'un script malveillant.
* **_Incohérences Géospatiales_** : Repérage de transactions réalisées à partir de localisations géographiques incompatibles avec le profil habituel ou les délais de transport physiques.
##  **4. Anomalies Transactionnelles Systémiques**
* **_Vitesse Transactionnelle ($\Delta t$)_** : Surveillance de la vélocité excessive des mouvements de fonds entre comptes, signalant souvent des tentatives de vidage de compte ou de transit rapide.
* **_Analyses sémantiques Document AI_** : Confrontation automatique entre les flux financiers (ISO 20022) et les documents justificatifs (factures, contrats) pour détecter les discordances de montants ou de dates.
* **_Note sur la conformité_** : Chaque détection est soumise à une revue humaine obligatoire (Workflow HITL) lorsque le score de suspicion se situe entre 80% et 95%, garantissant ainsi le respect du droit à l'explication prévu par le RGPD.