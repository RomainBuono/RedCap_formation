# 🎓 Travaux Dirigés — REDCap : Data Management en Recherche Clinique

> **Objectif** : Acquérir les compétences fondamentales de paramétrage et d'administration d'un projet REDCap.
> **Barème** : 40 questions — 1 bonne réponse = **0.5 point** — Note finale sur **20**.
> **Pré-requis** : Accès à une instance REDCap (mode *Development*).

---

## 📋 Grille de notation

| Tranche de score | Appréciation |
|---|---|
| 36–40 / 40 (18–20) | Excellent — Maîtrise complète |
| 28–35 / 40 (14–17.5) | Bien — Quelques lacunes mineures |
| 20–27 / 40 (10–13.5) | Passable — Révisions nécessaires |
| < 20 / 40 (< 10) | Insuffisant — Reprendre le module |

**Comptez vos bonnes réponses et divisez par 2 pour obtenir votre note sur 20.**

---

## 🔹 Partie 1 — Paramétrage des variables (Q1–Q6)

---

### Question 1 — Types de champs

Dans un essai clinique en oncologie, vous devez collecter la **localisation tumorale primitive en texte libre** (ex : "sein droit", "poumon gauche lobe supérieur"). Quel type de champ (`Field Type`) est le plus approprié ?

- [ ] A. Notes Box (Paragraph Text)
- [ ] B. Text Box (Short Text)
- [ ] C. Drop-down List
- [ ] D. Descriptive Text

<details>
<summary>✅ Voir la solution</summary>

**Réponse : B. Text Box (Short Text)**

Le `Text Box` est le type adapté pour une saisie libre courte (localisation, identifiant, code). Le `Notes Box` est réservé aux saisies longues (comptes-rendus, commentaires cliniques). Le `Drop-down` serait pertinent si la liste des localisations était prédéfinie et fermée, mais ici l'énoncé précise "texte libre". Le `Descriptive Text` n'est pas un champ de saisie, c'est un élément d'affichage (titre, consigne).

</details>

---

### Question 2 — Validation des champs texte

Vous souhaitez collecter le **taux de marqueur tumoral CA 19-9** (valeur décimale, ex : `37.5`) dans un champ `Text Box`. Quelle validation appliquez-vous ?

- [ ] A. `integer`
- [ ] B. `number`
- [ ] C. `alpha_only`
- [ ] D. Aucune validation n'est nécessaire

<details>
<summary>✅ Voir la solution</summary>

**Réponse : B. `number`**

La validation `number` accepte les valeurs décimales (ex : `37.5`), tandis que `integer` n'accepte que les nombres entiers. Pour un marqueur tumoral, les valeurs décimales sont fréquentes. `alpha_only` n'accepte que des lettres. Ne pas valider un champ numérique expose à des erreurs de saisie (texte libre, virgule au lieu du point) impossibles à détecter automatiquement en aval.

</details>

---

### Question 3 — Identifiant unique (Record ID)

Lors de la création d'un nouveau projet REDCap pour un essai clinique en oncologie, REDCap génère automatiquement un champ. Lequel ?

- [ ] A. Le champ `diagnostic_principal`
- [ ] B. Le champ `record_id` (identifiant unique de l'enregistrement)
- [ ] C. Le champ `date_inclusion`
- [ ] D. Un champ de consentement

<details>
<summary>✅ Voir la solution</summary>

**Réponse : B. Le champ `record_id`**

Tout projet REDCap contient obligatoirement un premier champ qui sert d'identifiant unique pour chaque enregistrement. Par défaut, il s'appelle `record_id`. Il est toujours placé en premier sur le premier instrument. Ce champ ne peut pas être supprimé mais peut être renommé.

</details>

---

### Question 4 — Nommage des variables

Parmi les noms de variables suivants, lequel est **invalide** dans REDCap ?

- [ ] A. `date_diagnostic`
- [ ] B. `taille_tumeur_mm`
- [ ] C. `Stade TNM`
- [ ] D. `score_ecog`

<details>
<summary>✅ Voir la solution</summary>

**Réponse : C. `Stade TNM`**

Les noms de variables REDCap (`Variable Name`) doivent respecter les règles suivantes : uniquement des lettres minuscules, des chiffres et des underscores (`_`). Pas d'espaces, pas de caractères spéciaux, pas de majuscules. `Stade TNM` contient un espace et des majuscules. Le nom valide serait `stade_tnm`. Le `Variable Name` est distinct du `Field Label` qui, lui, peut contenir du texte libre visible par l'utilisateur (ex : "Stade TNM").

</details>

---

### Question 5 — Variable catégorielle (Drop-down List)

Vous devez collecter le **statut de la réponse tumorale** selon les critères RECIST, avec les modalités suivantes : `1, Réponse complète | 2, Réponse partielle | 3, Maladie stable | 99, Non disponible`. Une seule réponse est possible. Quel paramétrage dans l'Online Designer est correct ?

- [ ] A. Field Type = `Radio Buttons`, Choices = `1, Réponse complète | 2, Réponse partielle | 3, Maladie stable | 99, Non disponible`
- [ ] B. Field Type = `Drop-down List`, Choices = `1, Réponse complète | 2, Réponse partielle | 3, Maladie stable | 99, Non disponible`
- [ ] C. Field Type = `Checkboxes`, Choices = `1, Réponse complète | 2, Réponse partielle | 3, Maladie stable | 99, Non disponible`
- [ ] D. Field Type = `Text Box` avec validation `integer`

<details>
<summary>✅ Voir la solution</summary>

**Réponse : B. Field Type = `Drop-down List`**

La `Drop-down List` est le type le plus adapté pour une variable catégorielle à réponse unique avec un nombre modéré de modalités. Elle évite les erreurs de saisie (pas de texte libre), prend moins de place à l'écran que les `Radio Buttons`, et guide l'utilisateur avec un menu déroulant. Les `Checkboxes` (C) permettraient de cocher plusieurs réponses, ce qui est incohérent avec RECIST (une seule réponse par évaluation). Le `Text Box` (D) obligerait à saisir manuellement le code, ce qui est source d'erreurs.

**Point clé** : la modalité `99, Non disponible` est codée directement dans les choix du champ. C'est une approche valide lorsque l'on veut distinguer "non évalué" des Missing Data Codes globaux du projet. La syntaxe des choix est : chaque modalité sur une ligne dans le format `valeur_codée, label_affiché`.

</details>

---

### Question 6 — Champ à choix multiples (Checkboxes)

Vous devez collecter les **toxicités post-chimiothérapie** d'un patient (il peut en présenter plusieurs simultanément). Quel type de champ choisissez-vous ?

- [ ] A. Radio Buttons (Single Answer)
- [ ] B. Drop-down List (Single Answer)
- [ ] C. Checkboxes (Multiple Answers)
- [ ] D. Text Box

<details>
<summary>✅ Voir la solution</summary>

**Réponse : C. Checkboxes (Multiple Answers)**

Les `Radio Buttons` et `Drop-down` ne permettent qu'une seule réponse. Les `Checkboxes` permettent de cocher plusieurs options (ex : nausées + neutropénie + asthénie). **Point d'attention** : dans REDCap, un champ checkbox se décompose à l'export en autant de variables binaires (0/1) qu'il y a de modalités. Par exemple, `toxicites___1`, `toxicites___2`, `toxicites___3` (noter les **3 underscores**).

</details>

---

## 🔹 Partie 2 — Dates, décimales et différences FR/US (Q7–Q12)

---

### Question 7 — Format de date : système français

Vous travaillez dans un centre de lutte contre le cancer **français** (CLCC). Quelle validation de date choisissez-vous pour un champ `date_inclusion` dans un essai clinique ?

- [ ] A. `date_ymd` (YYYY-MM-DD)
- [ ] B. `date_mdy` (MM-DD-YYYY)
- [ ] C. `date_dmy` (DD-MM-YYYY)
- [ ] D. `datetime_seconds_ymd`

<details>
<summary>✅ Voir la solution</summary>

**Réponse : C. `date_dmy` (DD-MM-YYYY)**

En France, la convention est JJ/MM/AAAA (jour-mois-année). REDCap supporte ce format via la validation `date_dmy`. **Attention** : quelle que soit la validation choisie à l'affichage, REDCap stocke **toujours** les dates en interne au format ISO `YYYY-MM-DD`. C'est une source fréquente de confusion lors des exports.

</details>

---

### Question 8 — Format de date : export des données

Après avoir saisi la date `25-12-2024` dans un champ validé `date_dmy`, sous quel format cette date apparaîtra-t-elle dans le fichier CSV exporté ?

- [ ] A. `25-12-2024`
- [ ] B. `2024-12-25`
- [ ] C. `12-25-2024`
- [ ] D. Le format dépend des paramètres régionaux du PC

<details>
<summary>✅ Voir la solution</summary>

**Réponse : B. `2024-12-25`**

REDCap exporte **toujours** les dates au format `YYYY-MM-DD` (ISO 8601), indépendamment du format de saisie choisi. C'est un point fondamental pour le data management : il faut toujours documenter cette conversion dans le plan de gestion des données et en informer le statisticien.

</details>

---

### Question 9 — Séparateur décimal

Dans REDCap, quel est le séparateur décimal utilisé pour les champs numériques ?

- [ ] A. La virgule `,` (convention française)
- [ ] B. Le point `.` (convention anglo-saxonne)
- [ ] C. Les deux sont acceptés indifféremment
- [ ] D. Cela dépend de la langue du projet

<details>
<summary>✅ Voir la solution</summary>

**Réponse : B. Le point `.` (convention anglo-saxonne)**

REDCap utilise **exclusivement** le point comme séparateur décimal (ex : `72.5` et non `72,5`). C'est un point d'attention majeur pour les utilisateurs français. Il faut le mentionner clairement dans les procédures de saisie et les formations des investigateurs. La virgule provoquera une erreur de validation ou un comportement inattendu.

</details>

---

### Question 10 — Validation numérique avec bornes

Dans le cadre d'un **bilan nutritionnel pré-chimiothérapie**, vous collectez le **poids en kg** (champ `poids`). Vous souhaitez que les valeurs soient comprises entre 30 et 200 kg. Comment configurez-vous cela ?

- [ ] A. Validation `number`, Min = `30`, Max = `200`
- [ ] B. Validation `integer`, Min = `30`, Max = `200`
- [ ] C. Action tag `@FORCE-MINMAX` sans validation
- [ ] D. Branching logic `[poids] >= 30 and [poids] <= 200`

<details>
<summary>✅ Voir la solution</summary>

**Réponse : A. Validation `number`, Min = `30`, Max = `200`**

On utilise `number` (et non `integer`) car le poids peut contenir des décimales (ex : 72.5 kg). Les champs Min/Max définissent les bornes de validation. **Par défaut**, REDCap affiche un avertissement hors bornes mais accepte la valeur. Pour **forcer** le refus, il faut ajouter l'action tag `@FORCE-MINMAX` en complément.

</details>

---

### Question 11 — Champ Date avec heure

Vous devez collecter la **date et l'heure d'administration** d'un traitement au format français. Quelle validation choisissez-vous ?

- [ ] A. `datetime_dmy` (DD-MM-YYYY HH:MM)
- [ ] B. `datetime_mdy` (MM-DD-YYYY HH:MM)
- [ ] C. `date_dmy` puis un champ séparé pour l'heure
- [ ] D. `time` uniquement

<details>
<summary>✅ Voir la solution</summary>

**Réponse : A. `datetime_dmy`**

REDCap propose des validations combinant date et heure (`datetime_dmy`, `datetime_seconds_dmy`, etc.). Il est préférable d'utiliser un seul champ `datetime_dmy` plutôt que deux champs séparés, car cela simplifie les calculs de durée et réduit les erreurs de saisie. Le format stocké sera `YYYY-MM-DD HH:MM`.

</details>

---

### Question 12 — Données manquantes codifiées

Pour un champ `statut_ecog` (Radio Button : `0, ECOG 0 | 1, ECOG 1 | 2, ECOG 2 | 3, ECOG 3 | 4, ECOG 4`), vous souhaitez ajouter les options *"Non renseigné"* et *"Non applicable"* en tant que données manquantes. Quelle fonctionnalité REDCap utilisez-vous ?

- [ ] A. Ajouter ces options comme choix normaux (ex : `3, Non renseigné | 4, Non applicable`)
- [ ] B. Utiliser la fonctionnalité **Missing Data Codes** du projet
- [ ] C. Laisser le champ vide et documenter dans le data dictionary
- [ ] D. Utiliser un champ `Notes Box` séparé

<details>
<summary>✅ Voir la solution</summary>

**Réponse : B. Utiliser la fonctionnalité Missing Data Codes**

REDCap permet de définir des **Missing Data Codes** au niveau du projet (dans *Project Setup > Additional Customizations*). Ces codes (ex : `NR` = Non Renseigné, `NA` = Non Applicable) apparaissent sur tous les champs comme options distinctes des réponses substantives. À l'export, ils sont identifiés séparément, ce qui permet une analyse statistique propre sans mélanger données manquantes et données réelles.

</details>

---

## 🔹 Partie 3 — Instruments et formulaires (Q13–Q16)

---

### Question 13 — Création d'un instrument

Dans l'Online Designer, que représente un **instrument** ?

- [ ] A. Un champ individuel du formulaire
- [ ] B. Un formulaire complet de collecte de données (un ensemble de champs)
- [ ] C. Un projet REDCap entier
- [ ] D. Un fichier PDF de consentement

<details>
<summary>✅ Voir la solution</summary>

**Réponse : B. Un formulaire complet de collecte de données**

Un **instrument** (ou formulaire) est une page regroupant un ensemble de champs liés thématiquement (ex : *Données démographiques*, *Examen clinique*, *Biologie*). Un projet contient un ou plusieurs instruments. Chaque instrument possède un champ de statut de complétion automatique (`instrument_complete`) qui prend les valeurs 0 (Incomplete), 1 (Unverified), 2 (Complete).

</details>

---

### Question 14 — Ordre des instruments

Comment modifie-t-on l'**ordre d'affichage** des instruments dans l'Online Designer ?

- [ ] A. En renommant les instruments par ordre alphabétique
- [ ] B. Par glisser-déposer (drag & drop) dans l'Online Designer
- [ ] C. En modifiant le Data Dictionary CSV manuellement
- [ ] D. B et C sont corrects

<details>
<summary>✅ Voir la solution</summary>

**Réponse : D. B et C sont corrects**

L'Online Designer permet de réordonner les instruments par glisser-déposer. Il est aussi possible de modifier l'ordre en réorganisant les lignes dans le Data Dictionary CSV, puis en le ré-important. Les deux méthodes sont valides ; la méthode visuelle (drag & drop) est plus intuitive, la méthode CSV est plus adaptée aux gros projets.

</details>

---

### Question 15 — Instruments répétés (Repeating Instruments)

Vous devez collecter les résultats de **plusieurs cycles de chimiothérapie** pour un même patient, chaque cycle ayant les mêmes variables. Quelle est la meilleure approche ?

- [ ] A. Créer un instrument par cycle (cycle_1, cycle_2, cycle_3…)
- [ ] B. Activer les **Repeating Instruments** pour un seul instrument "Cycle de chimiothérapie"
- [ ] C. Créer une variable texte et tout saisir en texte libre
- [ ] D. Utiliser plusieurs projets REDCap

<details>
<summary>✅ Voir la solution</summary>

**Réponse : B. Activer les Repeating Instruments**

Les **Repeating Instruments** (activables dans *Project Setup > Enable optional modules and customizations*) permettent de remplir un même formulaire plusieurs fois pour un même enregistrement. Chaque instance reçoit un numéro d'instance (`redcap_repeat_instance`). À l'export, une ligne supplémentaire est créée par instance. C'est la solution la plus propre et la plus scalable.

</details>

---

### Question 16 — Data Dictionary CSV

Le Data Dictionary CSV contient toutes les métadonnées d'un projet. Quelle colonne définit la **branching logic** d'un champ ?

- [ ] A. `field_note`
- [ ] B. `field_annotation`
- [ ] C. `branching_logic`
- [ ] D. `required_field`

<details>
<summary>✅ Voir la solution</summary>

**Réponse : C. `branching_logic`**

Le Data Dictionary est un fichier CSV avec des colonnes standardisées. La colonne `branching_logic` contient la condition d'affichage du champ. Les autres colonnes clés sont : `field_name`, `form_name`, `field_type`, `field_label`, `select_choices_or_calculations`, `field_annotation` (pour les action tags), `text_validation_type_or_show_slider_number`, `required_field`, etc.

</details>

---

## 🔹 Partie 4 — Branching Logic (Q17–Q22)

---

### Question 17 — Syntaxe de base

Vous avez un champ `rechute` (Radio : `1, Oui | 0, Non`). Vous souhaitez afficher le champ `date_rechute` **uniquement** si le patient a rechuté. Quelle branching logic appliquez-vous à `date_rechute` ?

- [ ] A. `[rechute] = "Oui"`
- [ ] B. `[rechute] = 1`
- [ ] C. `[rechute] = '1'`
- [ ] D. B et C sont tous les deux corrects

<details>
<summary>✅ Voir la solution</summary>

**Réponse : D. B et C sont tous les deux corrects**

En branching logic, on utilise la **valeur codée** (ici `1`), jamais le label (`Oui`). Les deux syntaxes `[rechute] = 1` et `[rechute] = '1'` sont acceptées par REDCap. La convention recommandée est d'utiliser les guillemets simples pour les valeurs catégorielles : `[rechute] = '1'`.

</details>

---

### Question 18 — Branching logic sur un checkbox

Vous avez un champ checkbox `toxicites` avec les choix `1, Nausées | 2, Neutropénie | 3, Neuropathie périphérique`. Vous souhaitez afficher un champ `grade_neutropenie` **uniquement** si "Neutropénie" est coché. Quelle syntaxe utilisez-vous ?

- [ ] A. `[toxicites] = '2'`
- [ ] B. `[toxicites(2)] = '1'`
- [ ] C. `[toxicites___2] = '1'`
- [ ] D. `[toxicites] contains 'Neutropénie'`

<details>
<summary>✅ Voir la solution</summary>

**Réponse : B. `[toxicites(2)] = '1'`**

C'est un **piège classique** de REDCap. Pour les checkboxes, la syntaxe de la branching logic utilise des **parenthèses** : `[nom_variable(code)] = '1'`. En revanche, à l'export, les variables checkbox deviennent `nom_variable___code` (3 underscores). Il ne faut pas confondre les deux syntaxes : parenthèses pour la logique REDCap, underscores pour l'export.

</details>

---

### Question 19 — Opérateur logique AND

Vous souhaitez afficher un champ `bilan_hepatique` uniquement si le patient a rechuté (`rechute = '1'`) **ET** que son score ECOG est supérieur à `2` (`score_ecog > 2`). Quelle branching logic écrivez-vous ?

- [ ] A. `[rechute] = '1' AND [score_ecog] > 2`
- [ ] B. `[rechute] = '1' and [score_ecog] > 2`
- [ ] C. `[rechute] = '1' && [score_ecog] > 2`
- [ ] D. Toutes les réponses ci-dessus sont valides

<details>
<summary>✅ Voir la solution</summary>

**Réponse : D. Toutes les réponses ci-dessus sont valides**

REDCap accepte `AND`, `and`, et `&&` de manière interchangeable pour le ET logique. De même pour le OU : `OR`, `or`, et `||` sont tous valides. La bonne pratique est de rester cohérent dans tout le projet (par convention, on utilise souvent `and` / `or` en minuscules pour la lisibilité).

</details>

---

### Question 20 — Branching logic avec opérateur OR

Le champ `avis_cardio` doit s'afficher si le patient a déclaré "Neutropénie" **OU** "Neuropathie périphérique" dans le checkbox `toxicites`. Quelle branching logic ?

- [ ] A. `[toxicites(2)] = '1' or [toxicites(3)] = '1'`
- [ ] B. `[toxicites] = '2' or [toxicites] = '3'`
- [ ] C. `[toxicites(2,3)] = '1'`
- [ ] D. `[toxicites(2)] = '1' and [toxicites(3)] = '1'`

<details>
<summary>✅ Voir la solution</summary>

**Réponse : A. `[toxicites(2)] = '1' or [toxicites(3)] = '1'`**

Chaque modalité d'un checkbox doit être testée individuellement avec la syntaxe `[variable(code)] = '1'`. Le `or` signifie que l'une OU l'autre condition suffit. La réponse D utilise `and`, ce qui exigerait les deux toxicités simultanément. La réponse B utilise une syntaxe invalide pour les checkboxes. La réponse C n'existe pas en REDCap.

</details>

---

### Question 21 — Branching logic imbriquée

Le champ `antiemetique_prescrit` doit s'afficher uniquement si le patient a des nausées (`[toxicites(1)] = '1'`) **ET** que le `grade_nausees` est `'3'` (grade 3 CTCAE). Quelle branching logic ?

- [ ] A. `[toxicites(1)] = '1'] and [grade_nausees] = '3'`
- [ ] B. `[toxicites(1)] = '1' and [grade_nausees] = '3'`
- [ ] C. `[grade_nausees] = '3'`
- [ ] D. `if([toxicites(1)] = '1', [grade_nausees] = '3', false)`

<details>
<summary>✅ Voir la solution</summary>

**Réponse : B. `[toxicites(1)] = '1' and [grade_nausees] = '3'`**

La branching logic de `antiemetique_prescrit` doit vérifier les **deux** conditions. La réponse A contient un crochet en trop (erreur de syntaxe). La réponse C est incomplète : si `grade_nausees` lui-même est conditionné par la toxicité "Nausées", il faut tout de même vérifier les deux niveaux dans la branching logic de `antiemetique_prescrit`. La réponse D utilise une syntaxe `if()` qui est réservée aux champs calculés, pas à la branching logic.

</details>

---

### Question 22 — Branching logic et valeur vide

Vous souhaitez afficher un champ `motif_non_inclusion` uniquement si le champ `inclus` (radio : `1, Oui | 0, Non`) vaut `0` **OU** est **vide** (non renseigné). Quelle branching logic ?

- [ ] A. `[inclus] = '0' or [inclus] = ''`
- [ ] B. `[inclus] = '0' or [inclus] = ""`
- [ ] C. `[inclus] != '1'`
- [ ] D. Toutes ces réponses produisent le même résultat

<details>
<summary>✅ Voir la solution</summary>

**Réponse : C. `[inclus] != '1'`**

L'opérateur `!=` (différent de) est la méthode la plus fiable pour inclure à la fois les valeurs vides et les autres valeurs. `[inclus] != '1'` sera vrai si la valeur est `0` **ou** si le champ est vide. Les syntaxes `= ''` et `= ""` pour tester la vacuité existent mais sont moins robustes et moins lisibles. La solution C est la bonne pratique recommandée.

</details>

---

## 🔹 Partie 5 — Action Tags (Q23–Q27)

---

### Question 23 — Masquer un champ sur la survey

Vous avez un champ `avis_rcp` (avis de la Réunion de Concertation Pluridisciplinaire) qui ne doit **pas** être visible par le patient sur le formulaire en ligne (survey de qualité de vie), mais doit rester visible pour l'ARC sur le formulaire de saisie (data entry form). Quel action tag utilisez-vous ?

- [ ] A. `@HIDDEN`
- [ ] B. `@HIDDEN-SURVEY`
- [ ] C. `@HIDDEN-FORM`
- [ ] D. `@READONLY`

<details>
<summary>✅ Voir la solution</summary>

**Réponse : B. `@HIDDEN-SURVEY`**

`@HIDDEN-SURVEY` masque le champ uniquement sur la page survey. Le champ reste visible et modifiable sur le data entry form. À l'inverse, `@HIDDEN-FORM` le masque uniquement sur le data entry form. `@HIDDEN` le masque **partout** (survey + form). `@READONLY` rend le champ visible mais non modifiable, ce qui est différent.

</details>

---

### Question 24 — Valeur par défaut

Vous souhaitez que le champ `centre_investigateur` ait comme valeur par défaut `'Centre Léon Bérard'` lors de la première ouverture du formulaire. Quel action tag ?

- [ ] A. `@DEFAULT='Centre Léon Bérard'`
- [ ] B. `@SETVALUE='Centre Léon Bérard'`
- [ ] C. `@PREFILL='Centre Léon Bérard'`
- [ ] D. `@NOW`

<details>
<summary>✅ Voir la solution</summary>

**Réponse : A. `@DEFAULT='Centre Léon Bérard'`**

`@DEFAULT` définit une valeur initiale qui n'est appliquée que lors du **premier chargement** d'un formulaire vierge. L'utilisateur peut la modifier. `@SETVALUE` est similaire mais écrase la valeur **à chaque chargement** de la page, même si une valeur existe déjà — c'est donc plus intrusif. `@PREFILL` n'existe pas. `@NOW` insère la date/heure courante.

</details>

---

### Question 25 — Horodatage automatique

Vous souhaitez enregistrer automatiquement la **date du jour** dans un champ `date_saisie` lors de la première saisie. Quel action tag ?

- [ ] A. `@NOW`
- [ ] B. `@TODAY`
- [ ] C. `@CALCDATE`
- [ ] D. `@DEFAULT='today'`

<details>
<summary>✅ Voir la solution</summary>

**Réponse : B. `@TODAY`**

`@TODAY` insère la date du jour (sans heure) dans un champ de type `Text Box` avec validation date. `@NOW` insère la date **et** l'heure, il faut donc un champ avec validation `datetime`. `@CALCDATE` est un action tag de calcul de dates (ajout/soustraction de durées). L'option D (`@DEFAULT='today'`) n'est pas une syntaxe valide.

</details>

---

### Question 26 — Champ en lecture seule

Vous avez un champ calculé `imc` qui ne doit **jamais** être modifié manuellement par l'utilisateur. Quel action tag ajoutez-vous ?

- [ ] A. `@HIDDEN`
- [ ] B. `@READONLY`
- [ ] C. `@DISABLED`
- [ ] D. Aucun, les champs calculés sont automatiquement en lecture seule

<details>
<summary>✅ Voir la solution</summary>

**Réponse : D. Aucun, les champs calculés sont automatiquement en lecture seule**

Les champs de type `calc` (Calculated Field) sont automatiquement en lecture seule sur les formulaires et les surveys. Il n'est pas nécessaire d'ajouter `@READONLY`. En revanche, si vous utilisez `@CALCTEXT` sur un champ `Text Box` pour simuler un calcul textuel, là il faudrait ajouter `@READONLY` car le champ est techniquement un champ texte modifiable.

</details>

---

### Question 27 — Combinaison d'action tags

Vous souhaitez qu'un champ `date_randomisation` soit **visible uniquement sur le data entry form**, **en lecture seule**, et affiche la **date du jour** à la première ouverture. Quelle combinaison d'action tags ?

- [ ] A. `@HIDDEN-SURVEY @READONLY @TODAY`
- [ ] B. `@HIDDEN-FORM @DEFAULT @NOW`
- [ ] C. `@HIDDEN @READONLY @TODAY`
- [ ] D. `@HIDDEN-SURVEY @TODAY`

<details>
<summary>✅ Voir la solution</summary>

**Réponse : A. `@HIDDEN-SURVEY @READONLY @TODAY`**

On combine : `@HIDDEN-SURVEY` (invisible sur la survey), `@READONLY` (non modifiable sur le data entry form), et `@TODAY` (date du jour au premier chargement). On peut placer plusieurs action tags dans le champ `Field Annotation`, séparés par des espaces. La réponse C utilise `@HIDDEN` qui masquerait le champ **partout**, y compris sur le form.

</details>

---

## 🔹 Partie 6 — Champs calculés et logique conditionnelle (Q28–Q32)

---

### Question 28 — Calcul de l'IMC

Vous avez les champs `poids` (en kg) et `taille` (en **mètres**). Quelle formule saisissez-vous dans le champ calculé `imc` ?

- [ ] A. `[poids] / [taille] * [taille]`
- [ ] B. `[poids] / ([taille] * [taille])`
- [ ] C. `[poids] / [taille]^2`
- [ ] D. `round([poids] / ([taille] * [taille]), 1)`

<details>
<summary>✅ Voir la solution</summary>

**Réponse : B. `[poids] / ([taille] * [taille])`**

La formule de l'IMC est poids / taille². REDCap **ne supporte pas** l'opérateur `^` pour la puissance. Il faut écrire `[taille] * [taille]` et utiliser des parenthèses pour la priorité des opérations. La réponse A est fausse car sans parenthèses, la division se fait avant la multiplication. La réponse D utilise `round()` qui est une bonne pratique pour arrondir, mais la question porte sur la formule de base.

</details>

---

### Question 29 — Fonction conditionnelle if()

Vous souhaitez un champ calculé `categorie_imc` qui affiche `1` si l'IMC < 18.5, `2` si l'IMC est entre 18.5 et 25, et `3` si l'IMC > 25. Quelle formule ?

- [ ] A. `if([imc] < 18.5, 1, if([imc] <= 25, 2, 3))`
- [ ] B. `if([imc] < 18.5, 1, 2, 3)`
- [ ] C. `case([imc], <18.5=1, <=25=2, >25=3)`
- [ ] D. `switch([imc], 18.5, 25)`

<details>
<summary>✅ Voir la solution</summary>

**Réponse : A. `if([imc] < 18.5, 1, if([imc] <= 25, 2, 3))`**

La fonction `if()` de REDCap accepte 3 arguments : `if(condition, valeur_si_vrai, valeur_si_faux)`. Pour plus de 2 catégories, on **imbrique** les `if()`. REDCap ne possède ni `case`, ni `switch`, ni de `if` à plus de 3 arguments. L'imbrication est la seule méthode.

</details>

---

### Question 30 — Calcul avec datediff()

Vous souhaitez calculer l'**âge en années** du patient à partir de `date_naissance` et `date_inclusion`. Quelle formule ?

- [ ] A. `datediff([date_naissance], [date_inclusion], 'y')`
- [ ] B. `datediff([date_naissance], [date_inclusion], 'y', true)`
- [ ] C. `datediff([date_naissance], [date_inclusion], 'y', 'dmy')`
- [ ] D. `datediff([date_inclusion], [date_naissance], 'y', 'dmy')`

<details>
<summary>✅ Voir la solution</summary>

**Réponse : C. `datediff([date_naissance], [date_inclusion], 'y', 'dmy')`**

La syntaxe de `datediff()` est : `datediff(date1, date2, unité, format_date)`. Les unités possibles sont `'y'` (années), `'M'` (mois), `'d'` (jours), `'h'` (heures), `'m'` (minutes), `'s'` (secondes). Le **4ème paramètre** spécifie le format de saisie des dates (`'dmy'`, `'mdy'`, `'ymd'`). Il est **indispensable** quand les champs utilisent un format non-YMD, sans quoi REDCap interprétera mal les dates.

</details>

---

### Question 31 — @CALCTEXT pour un résultat textuel

Vous souhaitez afficher le texte *"Majeur"* si `age >= 18` et *"Mineur"* sinon, dans un champ texte. Quelle solution ?

- [ ] A. Champ `calc` avec `if([age] >= 18, 'Majeur', 'Mineur')`
- [ ] B. Champ `text` avec action tag `@CALCTEXT(if([age] >= 18, 'Majeur', 'Mineur'))`
- [ ] C. Champ `radio` avec branching logic
- [ ] D. Champ `descriptive` avec piping

<details>
<summary>✅ Voir la solution</summary>

**Réponse : B. Champ `text` avec `@CALCTEXT`**

Un champ `calc` ne retourne que des **valeurs numériques**. Pour retourner du texte, on utilise l'action tag `@CALCTEXT` sur un champ `Text Box`. La syntaxe est `@CALCTEXT(if([age] >= 18, 'Majeur', 'Mineur'))`. Le champ se comporte alors comme un champ calculé en lecture seule, mais avec un résultat textuel.

</details>

---

### Question 32 — Calcul inter-instruments

Votre champ `poids` est sur l'instrument *Examen Clinique* et votre champ `taille` est sur l'instrument *Données Démographiques*. Le calcul de l'IMC fonctionnera-t-il en temps réel ?

- [ ] A. Oui, les calculs inter-instruments fonctionnent toujours en temps réel
- [ ] B. Non, le calcul ne se met à jour que lorsqu'on ouvre le formulaire contenant le champ calculé
- [ ] C. Non, les calculs inter-instruments sont impossibles
- [ ] D. Oui, à condition d'utiliser `@SETVALUE` plutôt qu'un champ `calc`

<details>
<summary>✅ Voir la solution</summary>

**Réponse : B. Le calcul se met à jour uniquement à l'ouverture du formulaire**

Les champs calculés REDCap se mettent à jour **en temps réel uniquement** pour les champs présents sur le **même** instrument. Si les champs référencés sont sur d'autres instruments, le calcul ne se rafraîchira qu'à la prochaine ouverture du formulaire contenant le champ calculé. Pour forcer la mise à jour globale, on peut exécuter la **Data Quality Rule H** (*Incorrect values for calculated fields*) depuis le module Data Quality.

</details>

---

## 🔹 Partie 7 — Surveys (Q33–Q35)

---

### Question 33 — Activation d'une survey

Pour qu'un instrument devienne accessible en tant que **questionnaire en ligne** (survey), que devez-vous faire ?

- [ ] A. Créer un nouveau projet de type "Survey"
- [ ] B. Activer le module "Use surveys in this project" dans Project Setup, puis activer la survey sur l'instrument souhaité
- [ ] C. Exporter l'instrument en PDF et l'envoyer par e-mail
- [ ] D. Ajouter l'action tag `@SURVEY` sur le premier champ

<details>
<summary>✅ Voir la solution</summary>

**Réponse : B. Activer les surveys dans Project Setup, puis activer par instrument**

La procédure est en 2 étapes : (1) activer *"Use surveys in this project"* dans **Project Setup** et (2) cliquer sur *"Enable"* à côté de l'instrument souhaité dans l'Online Designer. Chaque instrument peut être activé indépendamment comme survey. Un même instrument peut fonctionner à la fois comme data entry form et comme survey.

</details>

---

### Question 34 — Distribution d'une survey

Vous souhaitez envoyer un questionnaire de **qualité de vie (PRO — Patient-Reported Outcomes)** avec un lien personnalisé à chaque patient par e-mail. Quelle méthode utilisez-vous ?

- [ ] A. Le lien public de la survey (Public Survey Link)
- [ ] B. Le **Participant List** avec envoi d'invitations par e-mail via la **Survey Distribution Tool**
- [ ] C. Copier l'URL et l'envoyer manuellement par e-mail externe
- [ ] D. Le Survey Queue

<details>
<summary>✅ Voir la solution</summary>

**Réponse : B. Participant List + Survey Distribution Tool**

Le **Public Survey Link** génère un lien unique et anonyme qui crée un nouvel enregistrement à chaque accès — il ne permet pas de tracer qui a répondu. La **Participant List** permet d'associer un lien unique à chaque adresse e-mail, ce qui garantit la traçabilité et empêche les doublons. REDCap gère l'envoi, les relances, et le suivi des réponses directement depuis cette interface.

</details>

---

### Question 35 — Paramètres de survey

Parmi les paramètres suivants, lequel n'est **PAS** un paramètre de configuration d'une survey REDCap ?

- [ ] A. Texte de confirmation affiché après soumission
- [ ] B. Date d'expiration de la survey
- [ ] C. Nombre maximum de soumissions autorisées par participant
- [ ] D. Activation de la saisie vocale automatique

<details>
<summary>✅ Voir la solution</summary>

**Réponse : D. Activation de la saisie vocale automatique**

REDCap ne propose pas de fonctionnalité de saisie vocale. En revanche, les paramètres de survey incluent : texte de confirmation, date d'expiration, limite de réponses, sauvegarde partielle (*Save & Return Later*), barre de progression, affichage des questions (une page / question par question), personnalisation de l'en-tête et du pied de page, etc.

</details>

---

## 🔹 Partie 8 — Droits utilisateurs et administration (Q36–Q38)

---

### Question 36 — Rôles utilisateurs

Vous avez 10 ARC (Attachés de Recherche Clinique) qui doivent avoir exactement les mêmes droits. Quelle est la meilleure méthode ?

- [ ] A. Configurer les droits individuellement pour chaque ARC
- [ ] B. Créer un **User Role** "ARC" avec les droits appropriés, puis assigner chaque ARC à ce rôle
- [ ] C. Donner les droits d'administrateur à tous les ARC
- [ ] D. Utiliser un compte partagé unique pour tous les ARC

<details>
<summary>✅ Voir la solution</summary>

**Réponse : B. Créer un User Role**

Les **User Roles** permettent de définir un profil de droits réutilisable. On crée le rôle une fois, puis on y assigne les utilisateurs. Si les droits doivent évoluer, la modification du rôle se répercute automatiquement sur tous les utilisateurs assignés. Un compte partagé (D) est **interdit** en recherche clinique car il empêche la traçabilité individuelle (audit trail).

</details>

---

### Question 37 — Data Access Groups (DAGs)

Dans une étude **multicentrique**, comment restreindre l'accès des investigateurs de Lyon aux seules données de Lyon ?

- [ ] A. Créer un projet séparé par centre
- [ ] B. Utiliser les **Data Access Groups** (DAGs) et assigner chaque utilisateur à son groupe
- [ ] C. Utiliser la branching logic pour masquer les données des autres centres
- [ ] D. Créer un filtre d'export par centre

<details>
<summary>✅ Voir la solution</summary>

**Réponse : B. Data Access Groups (DAGs)**

Les **DAGs** cloisonnent les données au sein d'un même projet. Un utilisateur assigné au DAG "Lyon" ne voit que les enregistrements créés dans ce groupe. Les DAGs sont essentiels pour les études multicentriques car ils garantissent la confidentialité inter-centres tout en maintenant un seul projet centralisé pour le data management.

</details>

---

### Question 38 — Export des données

Lors d'un export au format CSV, quel traitement REDCap applique-t-il aux champs de type `Radio Button` et `Drop-down` ?

- [ ] A. Il exporte le label textuel (ex : `Oui`, `Non`)
- [ ] B. Il exporte la valeur codée (ex : `1`, `0`)
- [ ] C. Il propose les deux options : "raw" (codé) ou "label"
- [ ] D. Il exporte les deux dans deux colonnes séparées

<details>
<summary>✅ Voir la solution</summary>

**Réponse : C. Il propose les deux options**

Lors de l'export, REDCap demande le format souhaité : **Raw** (valeurs codées brutes) ou **Label** (labels textuels). Pour l'analyse statistique, on utilise généralement le format **Raw** car il est compatible avec les logiciels statistiques (R, SAS, Stata, Python). Le format **Label** est utile pour les rapports lisibles par les non-statisticiens.

</details>

---

## 🔹 Partie 9 — Dashboard et fonctionnalités avancées (Q39–Q40)

---

### Question 39 — Statut du projet

Quel est l'ordre correct du **cycle de vie** d'un projet REDCap ?

- [ ] A. Development → Production → Analysis/Cleanup → Completed
- [ ] B. Production → Development → Completed → Archive
- [ ] C. Development → Production → Completed
- [ ] D. Draft → Development → Production → Archive

<details>
<summary>✅ Voir la solution</summary>

**Réponse : A. Development → Production → Analysis/Cleanup → Completed**

Le cycle de vie est : **Development** (construction et tests avec données fictives), **Production** (collecte réelle — les modifications du data dictionary nécessitent une approbation), **Analysis/Cleanup** (fin de collecte, nettoyage des données, plus aucune modification structurelle), **Completed** (archivage, projet verrouillé). Il est aussi possible de passer un projet en statut **Inactive** ou de le supprimer.

</details>

---

### Question 40 — Record Status Dashboard

Le **Record Status Dashboard** affiche des icônes colorées pour chaque enregistrement et chaque instrument. Que signifie un cercle **rouge** ?

- [ ] A. L'instrument contient des erreurs de validation
- [ ] B. L'instrument est marqué comme **Incomplete** (des données existent mais le formulaire n'est pas finalisé)
- [ ] C. L'instrument n'a **aucune donnée** saisie
- [ ] D. L'instrument est verrouillé

<details>
<summary>✅ Voir la solution</summary>

**Réponse : B. Incomplete (des données existent mais le formulaire n'est pas finalisé)**

Le code couleur du Record Status Dashboard est :
- ⚪ **Gris** : aucune donnée saisie
- 🔴 **Rouge** : Incomplete (statut 0) — données partielles
- 🟡 **Jaune** : Unverified (statut 1) — données saisies mais non vérifiées
- 🟢 **Vert** : Complete (statut 2) — formulaire finalisé

Ce dashboard est l'outil principal de suivi de l'avancement de la saisie dans un projet.

</details>

---

## 📊 Feuille de score

Recopiez ce tableau et cochez vos bonnes réponses :

| Q | ✅ | Q | ✅ | Q | ✅ | Q | ✅ |
|---|---|---|---|---|---|---|---|
| 1 | ☐ | 11 | ☐ | 21 | ☐ | 31 | ☐ |
| 2 | ☐ | 12 | ☐ | 22 | ☐ | 32 | ☐ |
| 3 | ☐ | 13 | ☐ | 23 | ☐ | 33 | ☐ |
| 4 | ☐ | 14 | ☐ | 24 | ☐ | 34 | ☐ |
| 5 | ☐ | 15 | ☐ | 25 | ☐ | 35 | ☐ |
| 6 | ☐ | 16 | ☐ | 26 | ☐ | 36 | ☐ |
| 7 | ☐ | 17 | ☐ | 27 | ☐ | 37 | ☐ |
| 8 | ☐ | 18 | ☐ | 28 | ☐ | 38 | ☐ |
| 9 | ☐ | 19 | ☐ | 29 | ☐ | 39 | ☐ |
| 10 | ☐ | 20 | ☐ | 30 | ☐ | 40 | ☐ |

**Total de bonnes réponses : ___ / 40**

**Note finale : ___ / 40 × 0.5 = ___ / 20**

---

## 📚 Ressources

- [Site officiel REDCap](https://project-redcap.org/)
- [Vidéos de formation REDCap (Vanderbilt)](https://project-redcap.org/resources/videos/)
- [REDCap Community](https://projectredcap.org/resources/community/)
- [REDCap Help & FAQ (accessible depuis tout projet)](https://project-redcap.org/resources/)

---

> *Document créé pour la formation Data Management — Centre Léon Bérard*
> *Dernière mise à jour : Avril 2026*
