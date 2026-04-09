# 📋 Trame de paramétrage — Base REDCap « Formation»

> **Contexte** : Étude observationnelle prospective mono/multicentrique évaluant la prise en charge et la qualité de vie de patients atteints de tumeurs solides traités par chimiothérapie.
>
> **Objectif du TP** : Paramétrer entièrement cette base dans REDCap en mode *Development* en suivant les consignes ci-dessous. Chaque consigne renvoie aux notions vues dans le TD.

---

## 🔧 Paramètres globaux du projet

| Paramètre | Valeur à configurer |
|---|---|
| Titre du projet | `ONCOTD – Suivi patients chimio` |
| Purpose | `Research` |
| Use surveys | ✅ Activé |
| Repeating Instruments | ✅ Activé pour l'instrument *Cycle de chimiothérapie* |
| Missing Data Codes | `NR, Non renseigné \| NA, Non applicable` |
| Project notes | Documenter le contexte de l'étude |

---

## 📂 Instrument 1 — Inclusion

> **Objectif** : Collecter les données d'identification pseudonymisée, les dates clés, et le statut d'inclusion.
> **Concepts mobilisés** : Record ID, dates `dmy`, `datediff()`, `@CALCTEXT`, `@DEFAULT`, `@TODAY`, `@READONLY`, `@HIDDEN-SURVEY`, branching logic sur valeur vide (`!=`).

| # | Variable Name | Field Label | Field Type | Validation / Choices | Action Tags | Branching Logic | Notes |
|---|---|---|---|---|---|---|---|
| 1 | `record_id` | N° d'enregistrement | text | — | — | — | Champ auto-généré par REDCap. Ne pas supprimer. |
| 2 | `centre_investigateur` | Centre investigateur | text | — | `@DEFAULT='Centre Léon Bérard'` | — | Valeur pré-remplie au 1er chargement, modifiable. |
| 3 | `date_saisie` | Date de saisie | text | `date_dmy` | `@TODAY @READONLY @HIDDEN-SURVEY` | — | Horodatage automatique, invisible sur la survey, non modifiable. Combinaison de 3 action tags. |
| 4 | `date_inclusion` | Date d'inclusion | text | `date_dmy` | — | — | Format français JJ-MM-AAAA. Rappeler que l'export sera en YYYY-MM-DD. |
| 5 | `date_naissance` | Date de naissance | text | `date_dmy` | — | — | Servira au calcul de l'âge. |
| 6 | `age_inclusion` | Âge à l'inclusion (années) | calc | `datediff([date_naissance],[date_inclusion],'y','dmy')` | — | — | Calcul automatique. Résultat décimal. |
| 7 | `categorie_age` | Catégorie d'âge | text | — | `@CALCTEXT(if([age_inclusion]>=18,'Majeur','Mineur'))` | — | Démonstration de `@CALCTEXT` pour retourner du texte. |
| 8 | `inclus` | Le patient est-il inclus dans l'étude ? | radio | `1, Oui \| 0, Non` | — | — | Variable de filtrage. |
| 9 | `motif_non_inclusion` | Motif de non-inclusion | text | — | — | `[inclus] != '1'` | S'affiche si `inclus` = 0 **ou** vide. Utilisation de `!=`. |

---

## 📂 Instrument 2 — Données cliniques

> **Objectif** : Caractériser la tumeur et l'état général du patient.
> **Concepts mobilisés** : Text Box libre, Drop-down, Radio, validation `number`, branching logic simple, Missing Data Codes (via paramétrage projet).

| # | Variable Name | Field Label | Field Type | Validation / Choices | Action Tags | Branching Logic | Notes |
|---|---|---|---|---|---|---|---|
| 1 | `localisation_tumorale` | Localisation tumorale primitive (texte libre) | text | — | — | — | Saisie libre courte (ex : "sein droit"). |
| 2 | `stade_t` | Stade T (TNM) | dropdown | `0, T0 \| 1, T1 \| 2, T2 \| 3, T3 \| 4, T4 \| 99, Non évaluable` | — | — | Drop-down à réponse unique. Code `99` pour donnée non disponible intégrée dans les choix. |
| 3 | `stade_n` | Stade N (TNM) | dropdown | `0, N0 \| 1, N1 \| 2, N2 \| 3, N3 \| 99, Non évaluable` | — | — | Même logique que `stade_t`. |
| 4 | `stade_m` | Stade M (TNM) | dropdown | `0, M0 \| 1, M1 \| 99, Non évaluable` | — | — | — |
| 5 | `score_ecog` | Score ECOG (état général) | radio | `0, ECOG 0 – Activité normale \| 1, ECOG 1 – Restreint \| 2, ECOG 2 – Ambulatoire >50% \| 3, ECOG 3 – Alité >50% \| 4, ECOG 4 – Alité permanent` | — | — | Les Missing Data Codes (`NR`, `NA`) définis au niveau du projet apparaîtront automatiquement. Ne pas les ajouter dans les choix. |
| 6 | `ca_19_9` | Taux de CA 19-9 (U/mL) | text | `number` | — | — | Validation décimale. Rappeler l'utilisation du **point** comme séparateur décimal. |
| 7 | `rechute` | Rechute tumorale ? | radio | `1, Oui \| 0, Non` | — | — | Conditionne l'affichage de `date_rechute`. |
| 8 | `date_rechute` | Date de la rechute | text | `date_dmy` | — | `[rechute] = '1'` | Branching logic simple sur radio. |

---

## 📂 Instrument 3 — Bilan nutritionnel

> **Objectif** : Collecter les mesures anthropométriques et calculer l'IMC.
> **Concepts mobilisés** : Validation `number` avec bornes Min/Max, `@FORCE-MINMAX`, champs calculés (`calc`), `if()` imbriqué, calcul inter-instruments (à noter la limitation du temps réel).

| # | Variable Name | Field Label | Field Type | Validation / Choices | Action Tags | Branching Logic | Notes |
|---|---|---|---|---|---|---|---|
| 1 | `poids` | Poids (kg) | text | `number`, Min=`30`, Max=`200` | `@FORCE-MINMAX` | — | Bornes strictes. La virgule sera refusée, utiliser le point. |
| 2 | `taille` | Taille (m) | text | `number`, Min=`0.50`, Max=`2.50` | — | — | Saisir en **mètres** (ex : `1.72`). |
| 3 | `imc` | IMC (kg/m²) | calc | `[poids] / ([taille] * [taille])` | — | — | Champ calculé, lecture seule automatique. Pas d'opérateur `^`. |
| 4 | `categorie_imc` | Catégorie IMC | calc | `if([imc] < 18.5, 1, if([imc] <= 25, 2, 3))` | — | — | `1`=Dénutri, `2`=Normal, `3`=Surpoids/Obèse. Imbrication de `if()`. Penser à documenter dans le `Field Note`. |

---

## 📂 Instrument 4 — Cycle de chimiothérapie ⟳ *Repeating Instrument*

> **Objectif** : Collecter les données de chaque cycle de traitement. Le même formulaire sera rempli autant de fois que nécessaire (1 instance = 1 cycle).
> **Concepts mobilisés** : Repeating Instruments (`redcap_repeat_instance`), `datetime_dmy`.

⚠️ **Activer dans** : *Project Setup > Enable optional modules > Repeating instruments* → cocher cet instrument.

| # | Variable Name | Field Label | Field Type | Validation / Choices | Action Tags | Branching Logic | Notes |
|---|---|---|---|---|---|---|---|
| 1 | `protocole_chimio` | Protocole de chimiothérapie | dropdown | `1, FOLFOX \| 2, FOLFIRI \| 3, FOLFIRINOX \| 4, Gemcitabine \| 5, Carboplatine-Paclitaxel \| 6, Autre` | — | — | Drop-down à réponse unique. |
| 2 | `date_debut_cycle` | Date de début du cycle | text | `date_dmy` | — | — | — |
| 3 | `datetime_administration` | Date et heure d'administration | text | `datetime_dmy` | — | — | Champ combiné date+heure en format français. |
| 4 | `dose_administree` | Dose administrée (mg/m²) | text | `number`, Min=`0` | — | — | Dose en mg par m² de surface corporelle. |
| 5 | `cycle_commentaire` | Commentaire sur le cycle | notes | — | — | — | Notes Box pour commentaires libres longs. |

---

## 📂 Instrument 5 — Toxicités

> **Objectif** : Recueillir les toxicités et leurs grades selon la classification CTCAE.
> **Concepts mobilisés** : Checkboxes, branching logic sur checkbox `(code)`, opérateurs `and`/`or`, branching imbriquée.

| # | Variable Name | Field Label | Field Type | Validation / Choices | Action Tags | Branching Logic | Notes |
|---|---|---|---|---|---|---|---|
| 1 | `toxicites` | Toxicités observées (cocher toutes celles applicables) | checkbox | `1, Nausées/Vomissements \| 2, Neutropénie \| 3, Neuropathie périphérique \| 4, Asthénie \| 5, Diarrhée \| 6, Mucite` | — | — | Choix multiples. Export : `toxicites___1`, `toxicites___2`, etc. |
| 2 | `grade_nausees` | Grade des nausées (CTCAE) | dropdown | `1, Grade 1 \| 2, Grade 2 \| 3, Grade 3 \| 4, Grade 4` | — | `[toxicites(1)] = '1'` | Affiché uniquement si "Nausées" coché. Syntaxe **parenthèses**. |
| 3 | `antiemetique_prescrit` | Anti-émétique prescrit ? | radio | `1, Oui \| 0, Non` | — | `[toxicites(1)] = '1' and [grade_nausees] = '3'` | Branching **imbriquée** : nausées cochées ET grade 3. |
| 4 | `grade_neutropenie` | Grade de la neutropénie (CTCAE) | dropdown | `1, Grade 1 \| 2, Grade 2 \| 3, Grade 3 \| 4, Grade 4` | — | `[toxicites(2)] = '1'` | Affiché si "Neutropénie" coché. |
| 5 | `avis_cardio` | Avis cardiologique demandé ? | radio | `1, Oui \| 0, Non` | — | `[toxicites(2)] = '1' or [toxicites(3)] = '1'` | Opérateur **OR** : neutropénie ou neuropathie. |
| 6 | `bilan_hepatique` | Bilan hépatique réalisé ? | radio | `1, Oui \| 0, Non` | — | `[rechute] = '1' and [score_ecog] > 2` | Branching **inter-instruments** (variables des Données cliniques). |

---

## 📂 Instrument 6 — Évaluation tumorale

> **Objectif** : Documenter la réponse au traitement et l'avis de la RCP.
> **Concepts mobilisés** : Drop-down avec code `99`, `@HIDDEN-SURVEY`, combinaison d'action tags.

| # | Variable Name | Field Label | Field Type | Validation / Choices | Action Tags | Branching Logic | Notes |
|---|---|---|---|---|---|---|---|
| 1 | `reponse_recist` | Réponse tumorale (RECIST 1.1) | dropdown | `1, Réponse complète (RC) \| 2, Réponse partielle (RP) \| 3, Maladie stable (MS) \| 4, Progression (PD) \| 99, Non disponible` | — | — | Code `99` pour évaluation non réalisée. |
| 2 | `avis_rcp` | Avis de la RCP | notes | — | `@HIDDEN-SURVEY` | — | Visible uniquement sur le data entry form. Invisible pour le patient (survey). |
| 3 | `date_randomisation` | Date de randomisation | text | `date_dmy` | `@HIDDEN-SURVEY @READONLY @TODAY` | — | Triple action tag : caché sur survey + lecture seule + date auto. |

---

## 📂 Instrument 7 — Qualité de vie (PRO) 🔗 *Survey*

> **Objectif** : Auto-questionnaire patient (Patient-Reported Outcomes) évaluant la qualité de vie. Distribué via la Survey Distribution Tool.
> **Concepts mobilisés** : Activation survey, **matrice (Matrix Group)**, Radio Buttons en matrice, paramètres de survey, `@HIDDEN-FORM`.

⚠️ **Activer comme survey** : Online Designer → clic "Enable" sur cet instrument → configurer les paramètres :
- Texte de confirmation : *"Merci pour votre participation."*
- Save & Return Later : ✅ Activé
- Question display : *One section per page*

### Matrice — Intensité des symptômes (7 derniers jours)

Les 5 variables ci-dessous partagent le même **Matrix Group Name** = `matrice_symptomes` et les mêmes choix de réponse. Elles s'afficheront sous forme d'un tableau compact à l'écran.

| # | Variable Name | Field Label | Field Type | Choices (identiques) | Matrix Group Name | Action Tags | Notes |
|---|---|---|---|---|---|---|---|
| 1 | `qlq_douleur` | Douleur | radio | `0, Pas du tout \| 1, Un peu \| 2, Assez \| 3, Beaucoup` | `matrice_symptomes` | — | Ligne 1 de la matrice. |
| 2 | `qlq_fatigue` | Fatigue | radio | `0, Pas du tout \| 1, Un peu \| 2, Assez \| 3, Beaucoup` | `matrice_symptomes` | — | Ligne 2 de la matrice. |
| 3 | `qlq_nausees` | Nausées | radio | `0, Pas du tout \| 1, Un peu \| 2, Assez \| 3, Beaucoup` | `matrice_symptomes` | — | Ligne 3 de la matrice. |
| 4 | `qlq_appetit` | Perte d'appétit | radio | `0, Pas du tout \| 1, Un peu \| 2, Assez \| 3, Beaucoup` | `matrice_symptomes` | — | Ligne 4 de la matrice. |
| 5 | `qlq_insomnie` | Insomnie | radio | `0, Pas du tout \| 1, Un peu \| 2, Assez \| 3, Beaucoup` | `matrice_symptomes` | — | Ligne 5 de la matrice. |
| 6 | `score_symptomes` | Score total symptômes (/15) | calc | `[qlq_douleur] + [qlq_fatigue] + [qlq_nausees] + [qlq_appetit] + [qlq_insomnie]` | — | — | Somme automatique de la matrice. |
| 7 | `commentaire_patient` | Commentaire libre du patient | notes | — | — | — | Notes Box pour recueil qualitatif. |
| 8 | `date_evaluation_pro` | Date d'évaluation (ARC) | text | `date_dmy` | — | `@HIDDEN-SURVEY @TODAY` | Champ technique invisible pour le patient, rempli automatiquement côté ARC. |

---

## 📊 Synthèse des concepts couverts

| Concept du TD | Instrument(s) | Variable(s) exemple |
|---|---|---|
| Text Box | Inclusion, Données cliniques | `localisation_tumorale`, `ca_19_9` |
| Drop-down List | Données cliniques, Évaluation | `stade_t`, `reponse_recist` |
| Radio Buttons | Données cliniques, Toxicités | `score_ecog`, `rechute` |
| Checkboxes | Toxicités | `toxicites` |
| Notes Box | Cycle chimio, Qualité de vie | `cycle_commentaire`, `commentaire_patient` |
| Calc field | Bilan nutritionnel, Qualité de vie | `imc`, `categorie_imc`, `score_symptomes` |
| Matrice (Matrix) | Qualité de vie | `qlq_douleur` → `qlq_insomnie` |
| `date_dmy` | Inclusion, Données cliniques | `date_inclusion`, `date_rechute` |
| `datetime_dmy` | Cycle chimio | `datetime_administration` |
| `number` + Min/Max | Bilan nutritionnel | `poids` |
| `@DEFAULT` | Inclusion | `centre_investigateur` |
| `@TODAY` | Inclusion, Évaluation, QdV | `date_saisie`, `date_randomisation` |
| `@READONLY` | Inclusion, Évaluation | `date_saisie`, `date_randomisation` |
| `@HIDDEN-SURVEY` | Évaluation, Qualité de vie | `avis_rcp`, `date_evaluation_pro` |
| `@FORCE-MINMAX` | Bilan nutritionnel | `poids` |
| `@CALCTEXT` | Inclusion | `categorie_age` |
| `datediff()` | Inclusion | `age_inclusion` |
| `if()` imbriqué | Bilan nutritionnel | `categorie_imc` |
| Branching simple | Données cliniques | `date_rechute` |
| Branching checkbox `(code)` | Toxicités | `grade_nausees`, `grade_neutropenie` |
| Branching `and` | Toxicités | `antiemetique_prescrit`, `bilan_hepatique` |
| Branching `or` | Toxicités | `avis_cardio` |
| Branching `!=` (vide) | Inclusion | `motif_non_inclusion` |
| Branching inter-instruments | Toxicités | `bilan_hepatique` |
| Missing Data Codes | Projet global | Appliqué à `score_ecog` et tous les champs |
| Repeating Instruments | Cycle chimio | Tout l'instrument |
| Survey | Qualité de vie | Tout l'instrument |
| Data Dictionary CSV | Projet global | Import/export pour modifications en masse |
| Record ID | Inclusion | `record_id` |

---

> *Trame créée pour la formation Data Management — Centre Léon Bérard · Avril 2026*
