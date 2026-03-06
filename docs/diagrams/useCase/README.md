# **Diagramme de cas d'utilisation**<a href="../../"><img src="../../../assets/images/logo/linceul-audit-logo.webp" align="right" height="64"></a>
```mermaid
graph LR
    %% ── Acteurs internes
    NORA["👤 Nora\n«Analyste LCB-FT»"]
    EZRA["👤 Dr. Ezra ARIS\n«Data Scientist»"]
    AXEL["👤 Axel\n«DPO / Juriste»"]
    SOPHIE["👤 Sophie\n«Ingénieure SRE»"]

    %% ── Acteurs externes
    CBS["🏦 Core Banking\nSystem"]
    TRACFIN["🏛 TRACFIN"]
    ACPR["⚖ ACPR / CNIL"]

    subgraph SYSTEM ["◼ LINCEUL AUDIT — Système de Détection de Fraude Financière"]
        direction TB

        subgraph E1 ["Épic 1 — Ingestion & Prétraitement"]
            direction LR
            UC01(["US-01\nValider schéma JSON"])
            UC02(["US-02\nEnrichir données\nhistoriques"])
            UC03(["US-03\nImputer valeurs\nmanquantes"])
        end

        subgraph E2 ["Épic 2 — Inférence & Détection"]
            direction LR
            UC04(["US-04\nInférence ONNX\nPool dédié"])
            UC05(["US-05\nBascule mode\ndégradé"])
            UC06(["US-06\nParité\nPython/Java"])
        end

        subgraph E3 ["Épic 3 — Interface Analyste & HITL"]
            direction LR
            UC07(["US-07\nAfficher SHAP\nvalues"])
            UC08(["US-08\nOverride HITL\nQualifier alerte"])
            UC09(["US-09\nTxs similaires\npgvector"])
        end

        subgraph E4 ["Épic 4 — Audit & Conformité"]
            direction LR
            UC10(["US-10\nLog WORM\ncryptographique"])
            UC11(["US-11\nRapport PDF\nL561-15"])
            UC12(["US-12\nExtraction\nRGPD Art.12"])
        end

        subgraph E5 ["Épic 5 — Sécurité & MLOps"]
            direction LR
            UC13(["US-13\nChiffrer modèle\n.onnx AES-256"])
            UC14(["US-14\nDétecter inputs\nadversariaux"])
            UC15(["US-15\nAlerter dérive\nmodèle >5%"])
        end
    end

    %% ── Associations acteurs internes
    NORA --> UC01
    NORA --> UC07
    NORA --> UC08
    NORA --> UC09
    NORA --> UC11

    EZRA --> UC04
    EZRA --> UC06
    EZRA --> UC13
    EZRA --> UC15

    AXEL --> UC10
    AXEL --> UC11
    AXEL --> UC12

    SOPHIE --> UC05
    SOPHIE --> UC14
    SOPHIE --> UC15

    %% ── Associations acteurs externes
    CBS --> UC01
    CBS --> UC02
    TRACFIN --> UC11
    ACPR --> UC10
    ACPR --> UC12

    %% ── Relations include (flèche pointillée bleue)
    UC04 -.->|"«include»"| UC01
    UC04 -.->|"«include»"| UC02
    UC04 -.->|"«include»"| UC03
    UC07 -.->|"«include»"| UC04
    UC08 -.->|"«include»"| UC07
    UC08 -.->|"«include»"| UC09
    UC10 -.->|"«include»"| UC08
    UC11 -.->|"«include»"| UC10
    UC11 -.->|"«include»"| UC07

    %% ── Relations extend (flèche pointillée ambre)
    UC05 -.->|"«extend»&#10;latence > 500ms"| UC04
    UC14 -.->|"«extend»&#10;input atypique"| UC01
    UC15 -.->|"«extend»&#10;dérive > 5%"| UC06
    UC13 -.->|"«extend»&#10;déploiement modèle"| UC04

    %% ── Styles
    classDef actor  fill:#161b22,stroke:#58a6ff,color:#e6edf3,rx:6
    classDef actorX fill:#161b22,stroke:#484f58,color:#8b949e,rx:6
    classDef uc1    fill:#0f2a14,stroke:#238636,color:#e6edf3
    classDef uc2    fill:#0a1929,stroke:#1f6feb,color:#e6edf3
    classDef uc3    fill:#1a0f29,stroke:#8957e5,color:#e6edf3
    classDef uc4    fill:#291f0a,stroke:#d29922,color:#e6edf3
    classDef uc5    fill:#290a0a,stroke:#da3633,color:#e6edf3

    class NORA,EZRA,AXEL,SOPHIE actor
    class CBS,TRACFIN,ACPR actorX
    class UC01,UC02,UC03 uc1
    class UC04,UC05,UC06 uc2
    class UC07,UC08,UC09 uc3
    class UC10,UC11,UC12 uc4
    class UC13,UC14,UC15 uc5
```