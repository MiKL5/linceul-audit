# **CONTRIBUTING** <a href="./"><img src="assets/images/logo/linceul-audit-logo.webp" align="right" height="64"></a>
<div align="center">

[![Conventional Commits](https://img.shields.io/badge/Conventional%20Commits-1.0.0-yellow?style=flat&logo=conventionalcommits&logoColor=white)](https://conventionalcommits.org) 
[![GitFlow](https://img.shields.io/badge/Workflow-GitFlow%20Étendu-blue?style=flat&logo=git&logoColor=white)](docs/architecture) 
[![Java](https://img.shields.io/badge/java-25%20LTS-orange?style=flat&logo=openjdk&logoColor=white)](https://openjdk.org) 
[![Python](https://img.shields.io/badge/python-3.13-blue?style=flat&logo=python&logoColor=FFD43B)](https://python.org) 
[![AI Act](https://img.shields.io/badge/AI%20Act-Compliant-003399?style=flat&logo=europeancentralbank&logoColor=white)](docs/compliance) 
[![Classification](https://img.shields.io/badge/Classification-CONFIDENTIEL%20%2F%20HAUTE%20CRITICITÉ-red?style=flat)](docs/spec)

</div>

> Ce document constitue le **contrat de contribution** au projet `LINCEUL-2026`.  
> Tout contributeur est tenu de le lire **intégralement** avant toute modification du dépôt.  
> Le non-respect de ces règles peut entraîner le rejet immédiat d'une Pull Request et, dans les cas graves (fuite de secrets, contournement de conformité réglementaire), des conséquences juridiques.

---

## Table des matières

1. [Prérequis](#1-prérequis)
2. [Stratégie de branches](#2-stratégie-de-branches)
3. [Convention de commits](#3-convention-de-commits)
4. [Workflow Pull Request](#4-workflow-pull-request)
5. [Definition of Done](#5-definition-of-done)
6. [Standards de code](#6-standards-de-code)
7. [Stratégie de tests](#7-stratégie-de-tests)
8. [Règles de sécurité absolues](#8-règles-de-sécurité-absolues)
9. [Obligations de conformité](#9-obligations-de-conformité)

---

## 1. Prérequis

Avant de contribuer, les outils suivants doivent être installés et configurés :

Outil|Version minimale|Rôle
--|---|---
JDK|25 LTS|Runtime production (Project Loom + Panama FFM)
Python|3.13|Data Science, entraînement, export ONNX
PostgreSQL|18|Base de données + extension pgvector
Git|2.44+|Gestion de versions
GitHub CLI (`gh`)|2.x|Automatisation, création de branches, PRs
ONNX Runtime|Opset 21|Inférence Java via API FFM

L'authentification GitHub **doit** utiliser le protocole **SSH avec une clé `ed25519` protégée par passphrase**.  
Toute connexion HTTPS avec PAT est proscrite en dehors des pipelines CI/CD automatisés.

```bash
# Vérifier l'authentification avant de contribuer
gh auth status
ssh -T git@github.com   # Réponse attendue : "Hi MiKL5! You've successfully authenticated"
```

---

## 2. Stratégie de branches

Le projet suit un **GitFlow étendu** adapté à l'architecture hybride Java 25 / Python 3.13.

### 2.1 Branches permanentes (protégées — push direct interdit)

Branche|Rôle
---|---
`master`|Production certifiée — état reproductible et auditable (CMF L561-15). Chaque commit correspond à une version déployable en conditions judiciaires.
`develop`|Intégration continue — point de convergence de toutes les contributions avant stabilisation.

### 2.2 Nomenclature des branches temporaires

```
feature/us-XX-<description>    → Développement d'une User Story spécifique
ml/us-XX-<description>         → Data Science, entraînement, export ONNX, MLOps
infra/us-XX-<description>      → Infrastructure, CI/CD, observabilité, monitoring
docs/<sujet>                   → Documentation uniquement (aucun code fonctionnel)
release/vX.Y.Z                 → Gel du code et stabilisation avant production
hotfix/<description>           → Correctif critique sur master (branche éphémère)
chore/<description>            → Maintenance transversale (dépendances, rotation secrets)
```

**Règles de nommage :**
* Tout en minuscules, tirets (`-`) uniquement — jamais d'underscores ni d'espaces
* Toujours préfixé par le numéro de User Story si applicable (`us-01`)
* Maximum 60 caractères

**Exemples valides :**
```
feature/us-01-json-schema-validation
feature/us-04-inference-thread-pool
ml/us-06-onnx-parity-golden-dataset
infra/us-15-drift-alert-prometheus
docs/fria-ai-act-art27
hotfix/virtual-thread-pinning-regression
```

### 2.3 Cycle de vie d'une branche

```
master ◄─────────────────── release/vX.Y.Z ◄─── develop
                                                      ▲
                              feature/us-XX ──────────┤
                              ml/us-XX      ──────────┤
                              infra/us-XX   ──────────┤
                              docs/XXX      ──────────┘
```

> Les branches `hotfix/*` partent de `master` et sont mergées sur `master` **ET** `develop`.

---

## 3. Convention de commits

Le projet applique la spécification **[Conventional Commits 1.0.0](https://conventionalcommits.org)** de manière stricte.

### 3.1 Format

```
<type>(<scope>): <description impérative en minuscules>

[corps optionnel — pourquoi, pas comment]

[footer : Closes #US-XX, BREAKING CHANGE, Co-authored-by]
```

### 3.2 Types autorisés

Type | Usage | Exemple 
---|---|---
`feat`|Nouvelle fonctionnalité (User Story)|`feat(inference): add onnx thread pool isolation`
`fix`|Correction de bug|`fix(ingestion): reject null transaction amount field`
`test`|Ajout ou modification de tests|`test(parity): add python-java golden dataset cross-validation`
`docs`|Documentation uniquement|`docs(fria): add art27 fundamental rights assessment`
`chore`|Maintenance (dépendances, config)|`chore(deps): upgrade onnx-runtime to opset 21`
`refactor`|Refactorisation sans changement comportemental|`refactor(bulkhead): extract inference pool configuration`
`perf`|Optimisation de performance|`perf(loom): remove synchronized block causing pinning`
`ci`|Pipeline CI/CD|`ci(parity): add python-java score divergence check`
`security`|Correctif ou renforcement de sécurité|`security(model): encrypt onnx artifact at rest`
`compliance`|Modification liée à la conformité réglementaire|`compliance(rgpd): implement art22 hitl override audit log`

### 3.3 Règles absolues

* La **description** : anglais, impérative, minuscule initiale, sans point final
* Le **scope** : `ingestion` · `inference` · `hitl` · `audit` · `security` · `mlops` · `docs` · `ci`
* Les **breaking changes** : suffixe `!` après le type → `feat(api)!: change iso20022 transaction schema`
* Un commit = **une seule responsabilité logique** — les commits "chore+feat+fix" sont rejetés

---

## 4. Workflow Pull Request

### 4.1 Règles non négociables

* **Aucun push direct** sur `master` ou `develop` — toute violation entraîne un revert immédiat
* **Toute PR** doit référencer au moins une User Story (`Closes #US-XX` dans le footer)
* **Minimum 1 approbation** d'un reviewer désigné (cf. `.github/CODEOWNERS`) avant merge
* **Tous les checks CI** doivent être verts avant merge
* Les **conflits de merge** sont résolus par l'auteur de la PR — jamais par le reviewer

### 4.2 Processus étape par étape

```
1. Créer la branche depuis develop  →  git checkout -b feature/us-XX-description develop
2. Développer + commiter            →  Convention Conventional Commits
3. Push + ouvrir la PR              →  gh pr create --base develop
4. Remplir le template PR           →  .github/PULL_REQUEST_TEMPLATE.md (obligatoire)
5. Attendre les checks CI           →  Parity · Static Analysis · Unit Tests
6. Obtenir l'approbation            →  Minimum 1 reviewer
7. Merger                           →  Squash and merge (par défaut)
8. Supprimer la branche             →  Automatique après merge
```

### 4.3 Checklist PR minimale

* [ ] User Stories couvertes référencées
* [ ] Tests écrits et passants (coverage ≥ 80%)
* [ ] Test de parité Python/Java exécuté si inférence modifiée (delta < 1e-5)
* [ ] Aucun secret commité (`git diff --cached | grep -iE 'password|token|key|secret'`)
* [ ] `CHANGELOG.md` mis à jour
* [ ] Documentation impactée mise à jour

---

## 5. Definition of Done

Une User Story est **Done** si et seulement si **toutes** les conditions suivantes sont satisfaites :

<details>
<summary>🔍 Checklist complète DoD</summary>

### Code
* [ ] Compile sans warning (`javac -Xlint:all` / `python -W error`)
* [ ] Aucun bloc `synchronized` nu sur un chemin appelé par Virtual Threads (audit statique)
* [ ] Aucune valeur NaN/Null non gérée atteignant le moteur d'inférence
* [ ] Aucun secret en dur dans le code source ou les fichiers de configuration

### Tests
* [ ] Tests unitaires écrits et passants — couverture ≥ 80%
* [ ] Test de parité Python/Java sur le Golden Dataset (delta < 1e-5)
* [ ] Test de charge exécuté si le pipeline d'inférence est modifié
* [ ] Test de régression sur les scénarios de fraude de référence

### Conformité & Traçabilité
* [ ] Les logs de décision sont générés et conformes au format WORM
* [ ] Les SHAP values sont calculées et exposées à l'interface (si inférence modifiée)
* [ ] La User Story est tracée dans `CHANGELOG.md`
* [ ] La FRIA est mise à jour si le comportement du modèle est modifié

### Revue
* [ ] PR approuvée par au moins 1 reviewer désigné
* [ ] Tous les commentaires de review traités ou explicitement justifiés
* [ ] Branche rebased sur `develop` (0 commit de merge parasite)

</details>

---

## 6. Standards de code

### 6.1 Java 25

* **Indentation :** 4 espaces — aucune tabulation
* **Longueur de ligne :** 120 caractères maximum
* **Virtual Threads :** utilisation systématique pour toutes les opérations I/O-bound
* **FFM API (Panama) :** obligatoire pour tout interfaçage natif — JNI est interdit
* **🔴 Interdit :** `synchronized` sur un chemin appelé par un Virtual Thread (risque de Pinning)
* **🔴 Interdit :** secrets, tokens ou clés en dur — utiliser l'injection via KMS/Vault
* **Javadoc :** obligatoire sur toutes les méthodes publiques avec `@param`, `@return`, `@throws`
* **Formatter :** `google-java-format --aosp`

### 6.2 Python 3.13

* **Style :** PEP 8 strict, enforced par `ruff`
* **Type hints :** obligatoires sur toutes les fonctions (`def score(x: np.ndarray) -> float:`)
* **Docstrings :** format NumPy sur toutes les fonctions publiques
* **🔴 Interdit :** `import *` — imports explicites uniquement
* **Gestion des NaN :** toute fonction recevant un DataFrame doit valider l'absence de NaN/Null en entrée avant traitement
* **Export ONNX :** opset 21 minimum — validation de parité immédiate après export
* **Formatter :** `black` + `isort`

<details>
<summary>⚙️ Configuration des outils (pyproject.toml / .editorconfig)</summary>

```toml
# pyproject.toml
[tool.ruff]
line-length = 120
target-version = "py313"

[tool.black]
line-length = 120
target-version = ["py313"]

[tool.isort]
profile = "black"
```

```ini
# .editorconfig
[*.java]
indent_style = space
indent_size = 4
max_line_length = 120

[*.py]
indent_style = space
indent_size = 4
max_line_length = 120
```

</details>

---

## 7. Stratégie de tests

Conformément à la **section 8 du CDC**, le plan de tests est draconien et non négociable.

Niveau|Framework|Seuil de succès|Déclencheur
---|---|---|---
Tests unitaires|JUnit 5 / pytest|Coverage ≥ 80%|Chaque PR
Tests de parité|Script `tests/parity/`|Delta < 1e-5|PR modifiant l'inférence
Audit statique anti-pinning|SpotBugs + `jdk.VirtualThreadPinned`|0 violation|Chaque PR Java
Tests de charge|Gatling|5× charge nominale, 0 fuite mémoire| Release uniquement
Backtesting|Dataset 12 mois|AUC ≥ baseline établie|Avant chaque mise en production

> **Golden Dataset :** stocké dans `tests/fixtures/golden_dataset/` — immuable, versionné avec Git LFS.  
> Toute modification nécessite l'approbation du Data Scientist référent et une mise à jour de la Model Card.

---

## 8. Règles de sécurité absolues

Ces règles sont **non négociables**. Toute violation entraîne le rejet immédiat de la PR et une revue de sécurité.

* 🔴 **Zéro secret dans le code :** aucun mot de passe, token, clé API, IBAN, certificat ou chaîne de connexion en dur — injection obligatoire via KMS / HashiCorp Vault
* 🔴 **Zéro fichier `.env` commité :** `.env*` doit figurer dans `.gitignore` avant le premier commit
* 🔴 **Fichiers `.onnx` chiffrés :** les artefacts modèle doivent être chiffrés AES-256 au repos (US-13)
* 🔴 **Pas de données personnelles dans les logs :** PAN, IBAN, noms de clients, scores individuels ne doivent jamais apparaître en clair
* 🟡 **Sanitization des inputs :** toute entrée externe est validée et normalisée avant d'atteindre le moteur d'inférence (protection adversariale — US-14)
* 🟡 **Rate Limiting :** les endpoints d'inférence implémentent un rate limiting — protection contre l'inversion de modèle

---

## 9. Obligations de conformité

Réglementation |Obligation pour le contributeur
---|---
**AI Act Art. 27**|Toute modification du modèle ou de son périmètre d'usage met à jour `docs/compliance/FRIA.md`
**RGPD Art. 22**|Le mécanisme HITL ne peut jamais être désactivé ou contourné pour les scores dépassant le seuil critique
**CMF L561-15**|Les logs de décision sont WORM — toute modification du format de log nécessite une revue DPO préalable
**ISO/IEC 42001**|Les Model Cards (`docs/compliance/model-cards/`) sont mises à jour à chaque nouvelle version de modèle déployée

---

<div align="center">

[← README](README.md) · [Documentation](docs/) · [Sécurité](SECURITY.md)

___
© 2026 - Projet Linceul Audit. Tous droits réservés.
</div>