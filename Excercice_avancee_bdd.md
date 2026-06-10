# 📋 TD REDCap Avancé — Étude IMMUNORAD

> **Étude** : IMMUNORAD — Essai randomisé multicentrique international comparant radiothérapie seule (bras contrôle) vs immunothérapie + radiothérapie (bras intervention) dans le cancer bronchique non à petites cellules (CBNPC) localement avancé, stade III.
>
> **Design** : Étude interventionnelle, ouverte, randomisée 1:1, stratifiée par centre, longitudinale.
>
> **Centres investigateurs** :
> | Code | Centre | Pays |
> |---|---|---|
> | 1 | Centre Léon Bérard (CLB) — Lyon | France |
> | 2 | Institut Gustave Roussy (IGR) — Paris | France |
> | 3 | Royal Marsden Hospital — London | Royaume-Uni |
>
> **Étudiantes** : Mélodie et Joséphine construisent chacune leur propre projet REDCap et s'invitent mutuellement.

---

## 🎯 Objectifs pédagogiques

Ce TD couvre les thématiques avancées suivantes :

1. **User Rights** — Droits utilisateurs, rôles, Data Access Groups (DAGs)
2. **Multi-Language Management** — Interface bilingue français/anglais
3. **Extraction des données** — Export CSV (raw vs label)
4. **Reports** — Dashboard de suivi des inclusions (par centre et total)
5. **Data Quality** — Règles de cohérence temporelle
6. **Repeating Instruments** — Deux approches : répétition infinie vs événements définis
7. **Randomisation** — Import d'une table d'allocation pré-générée

---

## 🏗️ Architecture du projet

### Type de projet : Longitudinal

Ce projet utilise le mode **longitudinal** de REDCap. Contrairement au mode classique (un seul événement implicite), le mode longitudinal permet de définir des **événements** (visites) auxquels on rattache des instruments. C'est la structure standard pour un essai clinique avec des visites planifiées.

### Événements (Define My Events)

Configurer les événements suivants dans *Project Setup > Define My Events* :

| # | Event Name | Unique Event Name (auto) | Day Offset | Arm |
|---|---|---|---|---|
| 1 | Screening | `screening_arm_1` | 0 | Arm 1 |
| 2 | Randomisation | `randomisation_arm_1` | 1 | Arm 1 |
| 3 | Cycle 1 | `cycle_1_arm_1` | 21 | Arm 1 |
| 4 | Cycle 2 | `cycle_2_arm_1` | 42 | Arm 1 |
| 5 | Cycle 3 | `cycle_3_arm_1` | 63 | Arm 1 |
| 6 | Cycle 4 | `cycle_4_arm_1` | 84 | Arm 1 |
| 7 | Fin de traitement | `fin_traitement_arm_1` | 105 | Arm 1 |
| 8 | Suivi 6 mois | `suivi_6m_arm_1` | 285 | Arm 1 |

> **Remarque pédagogique** : L'instrument *Bilan clinique* est rattaché aux événements Cycle 1 → Suivi 6 mois. Il apparaîtra donc **6 fois** dans le parcours patient — c'est la méthode de répétition **par événements définis**. L'instrument *Événement indésirable* est quant à lui paramétré en **Repeating Instrument** (répétition infinie) : il pourra être rempli autant de fois que nécessaire à chaque visite.

### Designation des instruments par événement (Designate Instruments for My Events)

|  | Screening | Rando | C1 | C2 | C3 | C4 | EOT | FU 6m |
|---|---|---|---|---|---|---|---|---|
| **Inclusion** | ✅ | | | | | | | |
| **Randomisation** | | ✅ | | | | | | |
| **Bilan clinique** | | | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| **Événement indésirable** ⟳ | | | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| **Qualité de vie** 🔗 | ✅ | | | | | | ✅ | ✅ |

> ⟳ = Repeating Instrument (infini) · 🔗 = Survey activée

---

## 📂 Instrument 1 — Inclusion

> **Événement** : Screening
> **Concepts** : Record ID, Drop-down multicentrique, dates `dmy`, `datediff()`, `@CALCTEXT`, branching logic.

| # | Variable Name | Field Label | Field Type | Validation / Choices | Action Tags | Branching Logic | Notes |
|---|---|---|---|---|---|---|---|
| 1 | `record_id` | Patient ID | text | — | — | — | Auto-généré. |
| 2 | `centre` | Centre investigateur | dropdown | `1, Lyon – CLB \| 2, Paris – IGR \| 3, London – Royal Marsden` | — | — | **Champ de stratification** pour la randomisation. Servira aussi pour les DAGs et les Reports. |
| 3 | `date_screening` | Date de screening | text | `date_dmy` | `@TODAY` | — | — |
| 4 | `date_naissance` | Date de naissance / Date of birth | text | `date_dmy` | — | — | Label bilingue (cf. section Multi-Language). |
| 5 | `age` | Âge à l'inclusion | calc | `datediff([date_naissance],[date_screening],'y','dmy')` | — | — | — |
| 6 | `sexe` | Sexe / Sex | radio | `1, Homme / Male \| 2, Femme / Female` | — | — | — |
| 7 | `histologie` | Type histologique | dropdown | `1, Adénocarcinome \| 2, Carcinome épidermoïde \| 3, Carcinome à grandes cellules \| 4, Autre` | — | — | — |
| 8 | `stade_tnm` | Stade TNM | dropdown | `1, IIIA \| 2, IIIB \| 3, IIIC` | — | — | Critère d'éligibilité : stade III uniquement. |
| 9 | `score_ecog_baseline` | Score ECOG au screening | radio | `0, ECOG 0 \| 1, ECOG 1 \| 2, ECOG 2` | — | — | Critère : ECOG 0-2 pour inclusion. |
| 10 | `eligible` | Patient éligible ? | radio | `1, Oui / Yes \| 0, Non / No` | — | — | — |
| 11 | `motif_non_eligible` | Motif de non-éligibilité | text | — | — | `[eligible] != '1'` | Branching `!=` : affiché si Non ou vide. |
| 12 | `date_consentement` | Date de signature du consentement | text | `date_dmy` | — | `[eligible] = '1'` | — |
| 13 | `date_inclusion` | Date d'inclusion | text | `date_dmy` | — | `[eligible] = '1'` | Date de référence pour les règles Data Quality. |

---

## 📂 Instrument 2 — Randomisation

> **Événement** : Randomisation
> **Concepts** : Module Randomization de REDCap, table d'allocation CSV, stratification par centre.

| # | Variable Name | Field Label | Field Type | Validation / Choices | Action Tags | Branching Logic | Notes |
|---|---|---|---|---|---|---|---|
| 1 | `date_randomisation` | Date de randomisation | text | `date_dmy` | — | — | Doit être ≥ `date_inclusion` (règle Data Quality). |
| 2 | `bras` | Bras de randomisation | radio | `A, Contrôle – RT seule \| B, Intervention – Immuno + RT` | — | — | **Champ de randomisation**. Rempli automatiquement par le module Randomization. Ne pas saisir manuellement. |

### 🔧 Configuration du module Randomization

1. **Activer** : *Project Setup > Enable optional modules > Randomization* ✅
2. **Setup** : *Randomization > Setup*
   - Randomization field : `bras`
   - Stratification : ✅ Activée
   - Stratification field 1 : `centre`
   - Randomization model : `Stratified`
3. **Upload Allocation Table** : Importer le fichier `IMMUNORAD_Allocation_Table.csv` fourni.

> **Fonctionnement** : Quand l'ARC clique sur "Randomize", REDCap lit la ligne suivante non utilisée dans la table d'allocation **pour le centre du patient** et affecte automatiquement le bras. L'opération est irréversible et tracée dans l'audit trail.

---

## 📂 Instrument 3 — Bilan clinique

> **Événements** : Cycle 1, Cycle 2, Cycle 3, Cycle 4, Fin de traitement, Suivi 6 mois
> **Concepts** : Répétition **par événements** (un même instrument rattaché à plusieurs événements dans un projet longitudinal). Le formulaire est identique à chaque visite, mais chaque événement constitue une instance distincte.

| # | Variable Name | Field Label | Field Type | Validation / Choices | Action Tags | Branching Logic | Notes |
|---|---|---|---|---|---|---|---|
| 1 | `date_visite` | Date de la visite | text | `date_dmy` | — | — | Doit être ≥ `date_randomisation` (Data Quality). |
| 2 | `poids_visite` | Poids (kg) | text | `number`, Min=`30`, Max=`200` | — | — | Suivi pondéral longitudinal. |
| 3 | `score_ecog_visite` | Score ECOG à la visite | radio | `0, ECOG 0 \| 1, ECOG 1 \| 2, ECOG 2 \| 3, ECOG 3 \| 4, ECOG 4` | — | — | — |
| 4 | `reponse_tumorale` | Réponse tumorale (RECIST 1.1) | dropdown | `1, Réponse complète (RC) \| 2, Réponse partielle (RP) \| 3, Maladie stable (MS) \| 4, Progression (PD) \| 99, Non évaluée` | — | — | — |
| 5 | `scan_realise` | Scanner réalisé ? | radio | `1, Oui \| 0, Non` | — | — | — |
| 6 | `date_scan` | Date du scanner | text | `date_dmy` | — | `[scan_realise] = '1'` | — |
| 7 | `commentaire_visite` | Commentaire clinique | notes | — | — | — | — |

---

## 📂 Instrument 4 — Événement indésirable ⟳ *Repeating Instrument (infini)*

> **Événements** : Cycle 1 → Suivi 6 mois (tous les événements post-randomisation)
> **Concepts** : **Repeating Instrument** paramétré via *Project Setup > Enable Repeating Instruments* → cocher cet instrument pour tous les événements auxquels il est rattaché. Chaque EI est une nouvelle instance (`redcap_repeat_instance`).
>
> **Différence clé avec le Bilan clinique** : le Bilan clinique se répète un nombre **fixe** de fois (1 par événement planifié). L'instrument EI se répète un nombre **illimité** de fois à chaque événement — un patient peut avoir 0, 1, 5, ou 20 EI par visite.

| # | Variable Name | Field Label | Field Type | Validation / Choices | Action Tags | Branching Logic | Notes |
|---|---|---|---|---|---|---|---|
| 1 | `ei_type` | Type d'événement indésirable | dropdown | `1, Fatigue \| 2, Nausées \| 3, Pneumopathie radique \| 4, Pneumopathie immunologique \| 5, Dermatite radique \| 6, Thyroïdite \| 7, Hépatotoxicité \| 8, Neutropénie \| 9, Autre` | — | — | — |
| 2 | `ei_autre_detail` | Précisez (si Autre) | text | — | — | `[ei_type] = '9'` | — |
| 3 | `ei_grade` | Grade CTCAE v5.0 | dropdown | `1, Grade 1 \| 2, Grade 2 \| 3, Grade 3 \| 4, Grade 4 \| 5, Grade 5 (décès)` | — | — | — |
| 4 | `ei_date_debut` | Date de début de l'EI | text | `date_dmy` | — | — | Règle DQ : ≥ `date_inclusion`. |
| 5 | `ei_date_fin` | Date de fin de l'EI | text | `date_dmy` | — | — | Règle DQ : ≥ `ei_date_debut`. |
| 6 | `ei_imputabilite` | Imputabilité au traitement | dropdown | `1, Non lié \| 2, Peu probable \| 3, Possible \| 4, Probable \| 5, Certain` | — | — | — |
| 7 | `ei_grave` | EI grave (SAE) ? | radio | `1, Oui \| 0, Non` | — | — | — |
| 8 | `ei_action` | Action entreprise | dropdown | `1, Aucune \| 2, Réduction de dose \| 3, Interruption temporaire \| 4, Arrêt définitif \| 5, Hospitalisation` | — | `[ei_grade] >= '3'` | Affiché pour les grades ≥ 3. |
| 9 | `ei_description` | Description narrative | notes | — | — | — | — |

---

## 📂 Instrument 5 — Qualité de vie (PRO) 🔗 *Survey + Multi-Language + Matrice*

> **Événements** : Screening, Fin de traitement, Suivi 6 mois
> **Concepts** : Survey, matrice (Matrix Group), Multi-Language Management, `@HIDDEN-SURVEY`.

⚠️ **Activer comme survey** puis configurer :
- Texte de confirmation FR : *"Merci d'avoir complété ce questionnaire."*
- Texte de confirmation EN : *"Thank you for completing this questionnaire."*
- Save & Return Later : ✅
- Question display : *One section per page*

### Section 1 — Matrice des symptômes

Les 5 variables partagent le **Matrix Group Name** = `matrice_qlq` et les mêmes choix de réponse.

| # | Variable Name | Field Label (FR) | Field Label (EN) | Field Type | Choices FR / EN | Matrix Group |
|---|---|---|---|---|---|---|
| 1 | `qlq_douleur` | Douleur | Pain | radio | `0, Pas du tout / Not at all \| 1, Un peu / A little \| 2, Assez / Quite a bit \| 3, Beaucoup / Very much` | `matrice_qlq` |
| 2 | `qlq_fatigue` | Fatigue | Fatigue | radio | *(idem)* | `matrice_qlq` |
| 3 | `qlq_dyspnee` | Essoufflement | Breathlessness | radio | *(idem)* | `matrice_qlq` |
| 4 | `qlq_toux` | Toux | Cough | radio | *(idem)* | `matrice_qlq` |
| 5 | `qlq_appetit` | Perte d'appétit | Loss of appetite | radio | *(idem)* | `matrice_qlq` |

### Section 2 — Score et commentaire

| # | Variable Name | Field Label | Field Type | Validation / Choices | Action Tags | Notes |
|---|---|---|---|---|---|---|
| 6 | `score_qlq` | Score total (/15) | calc | `[qlq_douleur] + [qlq_fatigue] + [qlq_dyspnee] + [qlq_toux] + [qlq_appetit]` | — | Somme automatique. |
| 7 | `etat_global` | État de santé global (0–100) | text | `number`, Min=`0`, Max=`100` | — | Échelle visuelle analogique numérique. |
| 8 | `commentaire_qlq` | Commentaire libre | notes | — | — | — |
| 9 | `date_eval_qlq` | Date d'évaluation (ARC) | text | `date_dmy` | `@HIDDEN-SURVEY @TODAY` | Invisible patient. |

---

## 🔐 Étape 1 — User Rights et Data Access Groups

### 1.1 Créer les Data Access Groups (DAGs)

Dans *User Rights > DAGs*, créer :

| DAG Name | Centres associés |
|---|---|
| `Lyon_CLB` | Centre Léon Bérard |
| `Paris_IGR` | Institut Gustave Roussy |
| `London_RM` | Royal Marsden Hospital |

### 1.2 Créer les User Roles

Dans *User Rights*, créer les rôles suivants :

| Rôle | Droits |
|---|---|
| **Investigateur Principal** | Tous les droits sauf : Design, User Rights. Accès à son DAG uniquement. Export : Full. |
| **TRC (Technicien de Recherche Clinique)** | Data Entry : saisie + modification sur les instruments. Pas de droits de suppression, pas d'export, pas de randomisation, pas de User Rights, pas de Design. Accès à son DAG uniquement. |

### 1.3 Consignes pour Mélodie et Joséphine

**Mélodie** construit son projet et réalise les actions suivantes :
1. Créer les DAGs `Lyon_CLB`, `Paris_IGR`, `London_RM`
2. Créer le rôle `TRC`
3. S'attribuer le rôle **Investigateur Principal** sur le DAG `Lyon_CLB`
4. **Inviter Joséphine** avec le rôle `TRC` sur le DAG `Paris_IGR`
5. Vérifier que Joséphine ne peut voir que les données de Paris

**Joséphine** construit son propre projet et réalise les mêmes actions en miroir :
1. S'attribuer le rôle **Investigateur Principal** sur le DAG `Paris_IGR`
2. **Inviter Mélodie** avec le rôle `TRC` sur le DAG `Lyon_CLB`
3. Vérifier que Mélodie ne peut voir que les données de Lyon

### 1.4 Vérification croisée

Chaque étudiante se connecte au projet de l'autre et vérifie qu'elle :
- ✅ Peut saisir des données sur son DAG uniquement
- ✅ Ne voit pas les données des autres centres
- ❌ Ne peut pas supprimer d'enregistrements
- ❌ Ne peut pas exporter
- ❌ Ne peut pas modifier les droits utilisateurs
- ❌ Ne peut pas randomiser

---

## 🌐 Étape 2 — Multi-Language Management

### 2.1 Activation

Dans *Project Setup > Enable optional modules > Multi-Language Management* ✅

### 2.2 Configuration

1. **Langue par défaut** : Français (`fr`)
2. **Ajouter** : English (`en`)
3. Dans l'interface MLM, traduire **pour chaque champ** :
   - Le `Field Label` (ex : "Date de naissance" → "Date of birth")
   - Les `Choices` des Radio/Dropdown (ex : "Homme" → "Male")
   - Les `Section Headers` (ex : "Caractéristiques tumorales" → "Tumour characteristics")
   - Les textes de la survey (titre, instructions, confirmation)

### 2.3 Champs à traduire (liste minimale pour le TP)

| Variable | Label FR | Label EN |
|---|---|---|
| `date_naissance` | Date de naissance | Date of birth |
| `sexe` | Sexe | Sex |
| `sexe` (choix) | Homme / Femme | Male / Female |
| `eligible` | Patient éligible ? | Patient eligible? |
| `eligible` (choix) | Oui / Non | Yes / No |
| `qlq_douleur` | Douleur | Pain |
| `qlq_fatigue` | Fatigue | Fatigue |
| `qlq_dyspnee` | Essoufflement | Breathlessness |
| `qlq_toux` | Toux | Cough |
| `qlq_appetit` | Perte d'appétit | Loss of appetite |
| Choix matrice | Pas du tout / Un peu / Assez / Beaucoup | Not at all / A little / Quite a bit / Very much |

> **Point clé** : Le Multi-Language Management traduit l'**affichage** sans modifier les données stockées. Les valeurs codées (1, 2, 3…) restent identiques quelle que soit la langue. Un patient anglais qui répond "Male" et un patient français qui répond "Homme" génèrent tous les deux la valeur `1` dans la base.

---

## 📊 Étape 3 — Reports (Dashboard de suivi)

### 3.1 Report 1 — Inclusions par centre

*Data Exports, Reports, and Stats > Create New Report*

| Paramètre | Valeur |
|---|---|
| Report name | `Inclusions par centre` |
| Instruments | Inclusion |
| Fields | `record_id`, `centre`, `date_inclusion`, `eligible` |
| Filter Logic | `[eligible] = '1'` |
| Grouping / Stats | Activer *"Stats & Charts"* pour voir la répartition par `centre` |

**Rendu attendu** : Un tableau listant tous les patients inclus avec leur centre, et un graphique (pie chart / bar chart) montrant le nombre d'inclusions par centre.

### 3.2 Report 2 — Balance de randomisation

| Paramètre | Valeur |
|---|---|
| Report name | `Balance de randomisation` |
| Instruments | Inclusion + Randomisation |
| Fields | `record_id`, `centre`, `bras`, `date_randomisation` |
| Filter Logic | `[bras] != ''` |
| Grouping / Stats | Répartition par `bras` croisée avec `centre` |

**Rendu attendu** : Vérifier l'équilibre 1:1 par centre et globalement.

### 3.3 Report 3 — Suivi global des inclusions (Dashboard)

| Paramètre | Valeur |
|---|---|
| Report name | `Dashboard – Suivi global` |
| Fields | `record_id`, `centre`, `date_inclusion`, `bras`, `score_ecog_baseline`, `histologie` |
| Filter Logic | `[eligible] = '1'` |
| Order | `date_inclusion` (ascending) |

**Rendu attendu** : Vue d'ensemble de l'étude avec toutes les variables clés, triée chronologiquement. Permet de suivre le rythme d'inclusion.

---

## ✅ Étape 4 — Data Quality Rules

### 4.1 Règles à paramétrer

Dans *Data Quality > Custom Rules*, créer les règles suivantes :

| # | Nom de la règle | Logique REDCap | Description |
|---|---|---|---|
| 1 | Consentement avant inclusion | `datediff([date_consentement],[date_inclusion],'d','dmy') > 0` | La date d'inclusion doit être ≥ date de consentement. |
| 2 | Inclusion avant randomisation | `datediff([date_inclusion],[date_randomisation],'d','dmy') > 0` | La randomisation ne peut précéder l'inclusion. |
| 3 | Naissance avant screening | `datediff([date_naissance],[date_screening],'d','dmy') < 0` | Vérifie que la date de naissance est antérieure au screening. |
| 4 | Fin EI ≥ Début EI | `datediff([ei_date_debut],[ei_date_fin],'d','dmy') > 0` | La date de fin d'un EI ne peut pas précéder sa date de début. |
| 5 | EI après inclusion | `datediff([date_inclusion],[ei_date_debut],'d','dmy') > 0` | Un EI ne peut pas débuter avant la date d'inclusion. |

> **Attention** : Les règles `datediff` dans le module Data Quality retournent des résultats quand la condition est **violée**. Ajuster la logique en fonction : `datediff([date1],[date2],'d','dmy') > 0` retourne un résultat positif si date2 > date1.

### 4.2 Exécution

1. Aller dans *Data Quality*
2. Cliquer *"Execute"* sur chaque règle custom
3. Saisir volontairement **des données incohérentes** (ex : date de naissance après la date d'inclusion) pour vérifier que les règles détectent les erreurs
4. Corriger les données et relancer la vérification

---

## 📤 Étape 5 — Extraction des données

### 5.1 Export simple

*Data Exports, Reports, and Stats > All data > Export Data*

Réaliser **deux exports** et comparer les résultats :

| Export | Format | Résultat attendu |
|---|---|---|
| Export 1 | CSV / Microsoft Excel — **Raw** | Les valeurs codées brutes (ex : `sexe` = `1`, `bras` = `A`, dates en `YYYY-MM-DD`) |
| Export 2 | CSV / Microsoft Excel — **Labels** | Les labels textuels (ex : `sexe` = `Homme`, `bras` = `Contrôle – RT seule`, dates en `YYYY-MM-DD`) |

### 5.2 Points à observer

- **Dates** : Toujours exportées en `YYYY-MM-DD` quel que soit le format de saisie `dmy`.
- **Checkboxes** : Chaque modalité crée une colonne binaire (0/1).
- **Repeating instruments** : Les colonnes `redcap_event_name`, `redcap_repeat_instrument`, et `redcap_repeat_instance` apparaissent dans l'export. Une ligne par instance.
- **Projet longitudinal** : La colonne `redcap_event_name` distingue les événements (ex : `cycle_1_arm_1`, `fin_traitement_arm_1`).
- **DAGs** : La colonne `redcap_data_access_group` identifie le centre de chaque enregistrement.

### 5.3 Exercice

> Exporter les données, ouvrir le CSV dans un tableur, et identifier les colonnes `redcap_event_name`, `redcap_repeat_instrument`, `redcap_repeat_instance` et `redcap_data_access_group`. Vérifier que les dates sont au format ISO.

---

## 🔀 Étape 6 — Randomisation

### 6.1 Table d'allocation fournie

Le fichier `IMMUNORAD_Allocation_Table.csv` contient 90 allocations pré-générées par randomisation en blocs permutés (taille de bloc = 4), stratifiée par centre :

- 30 allocations pour Lyon (code `1`) → 15 bras A + 15 bras B
- 30 allocations pour Paris (code `2`) → 15 bras A + 15 bras B
- 30 allocations pour London (code `3`) → 15 bras A + 15 bras B

### 6.2 Format du fichier

Le CSV **ne contient pas de ligne d'en-tête**. Chaque ligne a 2 colonnes :

| Colonne 1 | Colonne 2 |
|---|---|
| Code du centre (`1`, `2`, ou `3`) | Bras alloué (`A` ou `B`) |

### 6.3 Procédure d'import

1. *Randomization > Setup* → Configurer (cf. Instrument 2)
2. *Randomization > Allocation Table* → Upload `IMMUNORAD_Allocation_Table.csv`
3. Vérifier que REDCap affiche le bon nombre d'allocations par strate
4. Tester : créer un patient sur le DAG Lyon, compléter l'inclusion, puis cliquer *"Randomize"* sur l'événement Randomisation

---

## 🔁 Récapitulatif — Deux approches de la répétition

| Critère | Répétition par événements | Repeating Instrument (infini) |
|---|---|---|
| **Exemple ici** | Bilan clinique (6 événements) | Événement indésirable |
| **Nombre d'instances** | Fixe (1 par événement) | Illimité |
| **Configuration** | Rattacher l'instrument à plusieurs événements dans *Designate Instruments* | Cocher l'instrument dans *Enable Repeating Instruments* |
| **Identifiant** | `redcap_event_name` | `redcap_repeat_instance` |
| **Cas d'usage** | Visites planifiées (cycles, bilans) | Données non planifiées (EI, traitements concomitants) |
| **À l'export** | 1 ligne par événement rempli | 1 ligne par instance |

---

## 📝 Checklist de rendu

| # | Tâche | Fait ? |
|---|---|---|
| 1 | Projet longitudinal créé avec 8 événements | ☐ |
| 2 | 5 instruments créés et rattachés aux bons événements | ☐ |
| 3 | Repeating Instrument activé pour *Événement indésirable* | ☐ |
| 4 | Survey activée pour *Qualité de vie* | ☐ |
| 5 | Multi-Language : français + anglais configurés | ☐ |
| 6 | 3 DAGs créés (Lyon, Paris, London) | ☐ |
| 7 | 2 rôles créés (IP, TRC) | ☐ |
| 8 | Invitation croisée réalisée et testée | ☐ |
| 9 | Table de randomisation importée | ☐ |
| 10 | ≥ 3 patients test saisis (1 par centre) avec randomisation | ☐ |
| 11 | ≥ 2 EI saisis sur un même patient à un même événement | ☐ |
| 12 | 3 Reports créés et fonctionnels | ☐ |
| 13 | 5 Data Quality rules créées et testées | ☐ |
| 14 | Export CSV réalisé (raw + labels) et colonnes vérifiées | ☐ |
| 15 | QdV remplie via le lien survey en anglais ET en français | ☐ |

---

> *TD créé pour la formation Data Management avancé — Centre Léon Bérard · Juin 2026*
