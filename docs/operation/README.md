# **🧐 Fonctionnement du Projet**<a href="../"><img src="../../assets/images/logo/linceul-audit-logo.webp" align="right" height="64"></a>
```mermaid
flowchart
  A[Ingestion & Normalisation\nJava 25 / Netty] --> B[Enrichissement KYC & Historique\nPostgreSQL + services internes]
  B --> C[Data Quality Gate\nContrôles AI Act]
  C -->|données valides| D[Inférence ONNX\nExecutor CPU-bound]
  C -->|données corrompues| X[Rejet / Erreur technique]

  D --> E[Scoring 0–100\nRègles de seuils]
  E -->|0–80| F[Validation automatique]
  E -->|80–95| G[Revue humaine\nWorkflow Human-in-the-Loop]
  E -->|95–100| H[Blocage préventif\nAlerte prioritaire]

  E --> I[Journalisation immuable\nLogs JSON sur WORM]
  G --> I
  H --> I
```