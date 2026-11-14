# 003 — Architecture et modèle de données

| Propriété | Valeur |
|---|---|
| Projet | **Ma Capture** |
| Nature | Document de conception technique et de modèle de données, **sans implémentation** |
| Version | **0.1** |
| Date | **14 novembre 2026** |
| État | Architecture générale arrêtée ; détails de sûreté différés au document 004 |
| Documents précédents | [001 — Vision et périmètre](001-vision-et-perimetre.md) · [002 — Parcours et ergonomie](002-parcours-utilisateurs-et-ergonomie.md) |
| Documents suivants | 004 — Intégrité, sauvegardes et cas limites ; 005 — Exigences et validation |

> **Règle de lecture :** **[VALIDÉ]** = décision explicite du cadrage ; **[ORIENTATION]** = détail proposé, ajustable au moment de la recette technique ; **[DOCUMENT 004]** = règle de récupération ou d'intégrité à détailler ensuite. Ce document n'autorise aucune mission de développement Codex.
>
> **Évolution par rapport aux documents 001 et 002 :** leurs points laissés ouverts sont tranchés ici lorsque c'est indiqué. En particulier, le stockage des annotations courantes **dans la fiche Capture**, la relation **1 capture ↔ 1 image**, le **SHA-256 avec le BLOB** et **l'absence de versionnage permanent** remplacent les anciennes possibilités exploratoires. Les deux premiers documents restent des documents de référence fonctionnelle.

## 1. Philosophie de l'architecture [VALIDÉ]

**Une petite application Windows locale, mono-utilisateur, avec une seule instance à la fois.**

Chaque composant fait ce qu'il sait faire de mieux :

- **L'interface** réalise les captures, affiche l'image et permet les annotations ainsi que la saisie documentaire.
- **Le système de fichiers** conserve provisoirement une capture non encore archivée, pour la retrouver après un plantage.
- **Une base SQLite de catalogue** gère les données métier et leurs relations.
- **Une base SQLite d'images** sert seulement d'entrepôt binaire, comme un dossier de fichiers, mais avec un identifiant entier plutôt qu'un chemin de fichier.
- **Le rendu graphique** assemble à l'affichage et à l'export l'image originale et la couche unique d'annotations.

L'application ne fonctionne pas en mode serveur, ne prévoit ni simultanéité multi-utilisateurs ni infrastructure distribuée. La complexité de conception doit rester proportionnée à ce contexte.

**Exclusions explicites :** serveur SQL, synchronisation cloud, GUID/UUID, transactions interbases complexes, `ATTACH DATABASE` comme mécanisme d'intégrité, moteur de versionnage et gestionnaire multi-calques.

**[ORIENTATION]** Python + PySide6 reste le choix d'implémentation privilégié pour l'interface et les objets graphiques, sous réserve de la validation technique ultérieure ; ce document ne lance aucun développement.

## 2. Stockage : deux bases ayant deux responsabilités [VALIDÉ]

```text
Données locales de Ma Capture
│
├── catalogue.db     → modèle documentaire / métier
│   ├── Captures
│   ├── Categories
│   ├── Tags
│   └── CaptureTags
│
├── images.db        → entrepôt binaire indépendant
│   └── Images(id, blob, sha256)
│
└── travail/         → provisoire récupérable
    ├── capture-en-cours.png
    └── capture-en-cours.json
```

Les noms de fichiers sont des **noms fonctionnels proposés**, non des chemins Windows imposés. Les fichiers auxiliaires éventuellement créés par SQLite selon son mode de journalisation ne changent pas cette séparation.

### 2.1 La base images n'est pas le modèle relationnel métier

La comparaison pertinente est celle d'un fichier enregistré sur disque : le catalogue conserverait son **chemin** ; ici, il conserve son **`image_id`**. Le BLOB est une ressource binaire indépendante.

- Aucun objet métier supplémentaire dans `images.db`.
- **Aucune clé étrangère entre les deux bases.**
- Pas d'attachement SQL obligatoire des bases pour travailler.
- La base `images.db` ne contient aucune catégorie, annotation, date ou information documentaire.
- Une recherche dans le catalogue ne charge pas les BLOB originaux.

Les relations internes au catalogue (capture/catégorie et capture/tags) restent des relations métier ordinaires ; leur gestion précise relève du schéma catalogue, sans étendre cette logique à l'entrepôt binaire.

### 2.2 Table d'entrepôt : trois colonnes seulement [VALIDÉ]

```sql
CREATE TABLE Images (
    id     INTEGER PRIMARY KEY,
    blob   BLOB NOT NULL,
    sha256 BLOB NOT NULL CHECK (length(sha256) = 32)
);
```

- `id` : identifiant numérique généré par SQLite.
- `blob` : octets exacts de l'image originale, normalement encodée en **PNG**.
- `sha256` : empreinte **SHA-256 binaire de 32 octets** calculée sur ces octets exacts.

L'empreinte n'est **pas** une clé primaire ni un GUID déguisé. Elle ne commande pas une déduplication générale. Deux images distinctes au sens métier peuvent avoir un contenu identique et donc la même empreinte.

La représentation **binaire** est retenue, pas les 64 caractères de l'écriture hexadécimale ni du Base64.

## 3. Modèle documentaire : catalogue.db [VALIDÉ / ORIENTATION]

### 3.1 Table principale Captures

La capture est l'objet documentaire principal, consulté et modifié le plus souvent. **L'état courant des annotations réside dans cette table.** Une jointure à une table d'annotations distincte n'est pas nécessaire en V0.

Schéma candidat (noms et types précis affinables sans changer les principes) :

```sql
CREATE TABLE Captures (
    id               INTEGER PRIMARY KEY,
    image_id         INTEGER NOT NULL UNIQUE,
    titre            TEXT NOT NULL,
    date_capture     TEXT NOT NULL,
    description      TEXT NOT NULL DEFAULT '',
    commentaire      TEXT NOT NULL DEFAULT '',
    categorie_id     INTEGER,
    annotations_json TEXT NOT NULL DEFAULT '[]',
    miniature        BLOB
);
```

**Décisions :**

- `id` est un entier généré automatiquement, indépendant du titre.
- `image_id` joue le rôle d'un **chemin de ressource binaire**. Pas de FK interbase.
- Le titre est prérempli par l'interface, mais modifiable à tout moment.
- La date de capture est produite automatiquement.
- `description` et `commentaire` sont documentaires, non dessinés sur l'image.
- Une capture possède **zéro ou une catégorie**.
- `annotations_json` contient le **résultat actuel**, prêt à charger sans rejouer d'historique.
- `miniature` est une petite image d'aperçu pour la bibliothèque ; elle ne remplace jamais le BLOB original.

**[ORIENTATION]** `image_id UNIQUE` matérialise la règle **une capture ↔ une image** dans le catalogue lui-même ; pas de partage de BLOB entre fiches en V0. Les dates seront stockées dans un format non ambigu (par exemple ISO 8601), avec affichage localisé.

### 3.2 Catégories et tags

```sql
CREATE TABLE Categories (
    id  INTEGER PRIMARY KEY,
    nom TEXT NOT NULL UNIQUE
);

CREATE TABLE Tags (
    id  INTEGER PRIMARY KEY,
    nom TEXT NOT NULL UNIQUE
);

CREATE TABLE CaptureTags (
    capture_id INTEGER NOT NULL,
    tag_id     INTEGER NOT NULL,
    PRIMARY KEY (capture_id, tag_id)
);
```

- Le champ `Captures.categorie_id` est facultatif et unique par fiche, ce qui suffit pour la relation « une catégorie au maximum ».
- Plusieurs tags peuvent être attachés à une capture via la table `CaptureTags`.
- La recherche et les filtres travaillent dans `catalogue.db` seulement.
- La suppression d'une capture entraîne aussi la suppression de ses lignes `CaptureTags` ; une catégorie ou un tag partagé n'est pas supprimé pour autant.

**[ORIENTATION]** Des index simples seront créés sur les champs réellement filtrés ou triés (date, titre, catégorie, tags), sans indexation prématurée de tous les champs. Les contraintes internes au catalogue seront définies lors du schéma SQL définitif.

### 3.3 Règles d'identifiants [VALIDÉ]

`INTEGER PRIMARY KEY` partout où un identifiant propre à une table est nécessaire. SQLite attribue l'entier en l'absence de valeur fournie.

- **Aucun GUID ni UUID.**
- **Pas** de générateur d'identifiant côté application.
- La réutilisation éventuelle de valeurs supprimées **n'a aucune importance métier**.
- Inutile d'imposer le mot-clé `AUTOINCREMENT` au sens particulier de SQLite.

Le terme « auto-incrémenté » est utilisé dans son **sens fonctionnel courant** : l'application ne choisit pas les ID ; SQLite les attribue.

## 4. Annotations : un JSON courant, aucun versionnage permanent [VALIDÉ]

Les annotations sont une couche logique d'objets graphiques **au-dessus de l'image originale immuable**, mais pas une pile de calques Photoshop.

Le JSON courant peut contenir une liste d'objets avec :
- un **type** : ligne, flèche, rectangle, ellipse ou texte ;
- une **géométrie** exprimée dans le repère de l'image originale ;
- une **couleur** et une **épaisseur** de trait ;
- un **texte**, lorsqu'il s'agit d'une annotation textuelle ;
- un ordre d'affichage si des objets se superposent.

Exemple **illustratif**, pas un format imposé :

```json
[
  {"type":"rectangle","x":120,"y":84,"largeur":260,"hauteur":90,"couleur":"#ff0000","epaisseur":3},
  {"type":"texte","x":125,"y":65,"texte":"À revoir","couleur":"#ff0000"}
]
```

Les rectangles et ellipses sont des **contours transparents**, jamais des aplats opaques. Leur déplacement, couleur, taille et texte restent modifiables lorsqu'on rouvre une capture.

**[VALIDÉ] Aucun historique permanent :**

- `Captures.annotations_json` contient **uniquement l'état enregistré actuel**.
- `Ctrl + Z` / `Ctrl + Y` agissent **dans la séance d'édition** ; leur pile est en mémoire et n'est pas une table métier.
- Un nouvel enregistrement remplace le JSON actuel.
- Aucune table `HistoriqueAnnotations`, aucun rejeu de commandes, aucun snapshot de version dans la V0.

Le JSON de **récupération provisoire**, s'il existe, est un filet de sécurité en cas d'interruption et **ne constitue pas du versionnage**.

## 5. Miniatures et performances de consultation [VALIDÉ / ORIENTATION]

**[ORIENTATION RETENUE POUR LA V0]** La miniature est stockée dans `Captures.miniature` : elle est proche des informations utilisées par la vue mosaïque et évite d'ouvrir l'image originale complète pour chaque vignette.

- Liste et mosaïque lisent le catalogue.
- L'image originale n'est chargée depuis `images.db` **qu'au moment où elle est nécessaire** (ouverture dans l'éditeur, rendu / export).
- La miniature peut être régénérée à partir de l'original si besoin ; elle n'est pas la référence documentaire.
- Éviter le chargement simultané d'une bibliothèque entière de BLOB complets.

**[À PRÉCISER]** Taille et encodage des miniatures, choix de les montrer avec ou sans annotations, actualisation après modification et pagination éventuelle. Ne pas surdimensionner ce sous-système avant d'en mesurer le besoin.

## 6. Capture de travail provisoire [ORIENTATION FORTE]

Une seule capture non encore archivée est éditée à la fois. Le but du provisoire est de **ne pas perdre la capture** si l'application s'interrompt avant l'enregistrement, et non de gérer une file de captures en rafale.

Support simple envisagé :

- `travail/capture-en-cours.png` : image originale immédiatement écrite dans un emplacement local persistant.
- `travail/capture-en-cours.json` : état provisoire léger : titre, données saisies et annotations, ainsi que, si utile, un identifiant d'image déjà insérée.

Le nom fixe est adapté **uniquement parce qu'une seule capture active non archivée est admise**. Une autre capture ne doit jamais écraser le provisoire courant sans passer par la gestion **Enregistrer / Ne pas enregistrer / Annuler** prévue au document 002.

**[VALIDÉ] Le provisoire ne doit jamais être détruit avant la validation de l'enregistrement définitif de la fiche `Captures`.**

Le mécanisme de persistance périodique du JSON, le choix de la localisation et les règles d'écriture sûre des fichiers sont renvoyés au document 004. Le provisoire n'est pas un répertoire d'images importées.

## 7. Cycle de vie : création, modification, export, suppression

### 7.1 Création et enregistrement initial [VALIDÉ]

Ordre logique de l'enregistrement :

```text
Capture réelle
    ↓
Écriture de capture-en-cours.png (+ JSON provisoire)
    ↓
L'utilisateur annote et/ou renseigne la fiche
    ↓
Enregistrer
    ↓
1. INSERT de l'image PNG + SHA-256 dans images.db
    ↓
2. INSERT de Captures (image_id + fiche + annotations) dans catalogue.db
    ↓
3. Validation réussie de la fiche
    ↓
4. Suppression des fichiers provisoires
```

Chaque écriture SQLite est validée dans **sa propre base** ; aucune transaction interbase n'est exigée. Si la seconde écriture échoue ou si l'application s'arrête entre les deux, le provisoire est conservé pour la récupération.

Le **SHA-256 est calculé sur les octets mêmes du PNG provisoire**, identiques à ceux insérés comme BLOB. Une réécriture ou une recompression différente des octets avant le calcul ferait perdre l'intérêt de la comparaison.

**[ORIENTATION]** Après insertion du BLOB, mémoriser son `image_id` dans le JSON provisoire lorsque possible : c'est une indication pratique pour reprendre une insertion interrompue. Le SHA permet de retrouver une image orpheline correspondante même si ce marqueur n'a pas été enregistré.

### 7.2 Modifications ultérieures [VALIDÉ]

- On rouvre une capture et son BLOB.
- On change les champs documentaires et/ou le JSON des annotations.
- `Enregistrer` met à jour **la fiche dans catalogue.db**, sans réécrire le BLOB immuable.
- L'original ne devient jamais une image aplatie par les annotations.

L'éventuelle restauration des **modifications non enregistrées d'une fiche déjà archivée** après plantage est à spécifier dans le document 004.

### 7.3 Export et presse-papiers [VALIDÉ]

L'application compose l'image originale et le JSON d'annotations courant ou visible, puis produit :
- un fichier PNG/JPEG, ou
- une image copiée **vers** le presse-papiers.

**Aucun chemin ni historique d'export** n'est inscrit dans les bases. Aucun fichier exporté n'est surveillé. Aucun import/collage d'image **entrant** n'est prévu.

### 7.4 Suppression [VALIDÉ]

**Une capture et son image ont le même cycle de vie.** Il ne s'agit pas d'un entrepôt mutualisé entre plusieurs fiches.

Ordre voulu :

1. supprimer le BLOB dans `images.db` ;
2. supprimer la fiche dans `catalogue.db`, ainsi que ses associations de tags.

Si une interruption se produit entre les deux, la fiche restée sans image sera automatiquement purgée au prochain contrôle de cohérence. Il n'y a **aucune FK interbase**.

La confirmation ergonomique d'une suppression volontaire reste à préciser ; la purge technique des incohérences au démarrage, elle, est **silencieuse**.

## 8. SHA-256 : reconnaître une insertion interrompue [VALIDÉ]

L'empreinte est ajoutée **à côté du BLOB** (et non uniquement dans `Captures`) pour pouvoir retrouver une image qui a été insérée **avant** que sa fiche soit créée.

**Usage précis :**

1. Au redémarrage, si un fichier `capture-en-cours.png` existe, calculer son SHA-256.
2. Parmi les images **orphelines** de l'entrepôt, rechercher une empreinte correspondante.
3. Si une image correspond, elle peut être réutilisée lors de l'achèvement de l'enregistrement, sans insérer un nouveau BLOB.
4. Sinon, la récupération repose sur le fichier provisoire, et le BLOB sera inséré lors d'un nouvel Enregistrer.

Le SHA est **un indice de correspondance de contenu**, pas un identifiant de capture ni une preuve que deux opérations métier sont identiques. Le marqueur provisoire `image_id` (s'il existe) aide à lever les cas de contenu binaire identique.

Le contrôle de cohérence **ne charge pas tous les BLOB pour les hacher**. Il lit d'abord les **identifiants** et utilise les SHA déjà stockés uniquement pour le cas de récupération.

## 9. Contrôle automatique au démarrage [VALIDÉ]

Au lancement, une vérification rapide rapproche :

- les `Images.id` de `images.db` ;
- les `Captures.image_id` de `catalogue.db`.

Elle se déroule pendant le chargement, **sans fenêtre de diagnostic ni intervention normale de l'utilisateur**.

**Ordre impératif : traiter la récupération provisoire avant d'effacer une image orpheline pouvant lui correspondre.**

| Situation | Action |
|---|---|
| Image et capture liées | Rien |
| Image sans capture, **correspondant au provisoire en attente** | **Conserver** pour la récupération |
| Image sans capture, **sans correspondance avec le provisoire** | **Supprimer silencieusement** |
| Capture sans image | **Supprimer silencieusement la capture** et ses associations |
| Fichier provisoire restant après enregistrement confirmé | **Nettoyer après vérification**, pas avant |

Le rôle du contrôle est d'éliminer de simples résidus d'une opération interrompue. Il n'est **pas** un moteur de restauration, d'audit ou de surveillance du système de fichiers.

**Point d'attention minimal :** une empreinte SHA peut être identique pour deux images au contenu rigoureusement identique. La vérification d'un ID déjà conservé dans le JSON provisoire prime lorsque disponible. On ne confond pas toutes les images ayant le même SHA avec une seule capture.

**[DOCUMENT 004]** Décrire l'ordre précis de détection, récupération, confirmation d'abandon, suppression sûre du provisoire et traitement d'un fichier provisoire incomplet. Pas de réparation sophistiquée ni journal de transactions applicatif demandé.

## 10. Sauvegarde, stockage local et limites [VALIDÉ / DOCUMENT 004]

- Données **locales** sur le poste ; une seule application active.
- Deux bases distinctes = **deux fichiers à sauvegarder ensemble de manière cohérente**.
- L'image provisoire est une donnée à préserver lorsqu'une capture non enregistrée existe.
- Les contrôles d'intégrité, les stratégies simples de sauvegarde/restauration et la politique de maintenance restent du ressort du document 004.
- Pas de surveillance des exports, de scan de répertoires externes, de serveur distant ou de synchronisation cloud.
- Aucun choix anticipé de journalisation SQLite uniquement pour faire fonctionner des transactions entre les deux bases.

**Attention au périmètre de la « réparation silencieuse » :** elle concerne les **références orphelines** constatées à l'ouverture, et non une corruption générale de base ou une réparation magique de pixels perdus.

## 11. Décisions récapitulatives

| Réf. | Décision |
|---|---|
| A-001 | Windows, application locale, mono-utilisateur et instance unique |
| A-002 | Deux bases indépendantes : `catalogue.db` et `images.db` |
| A-003 | `images.db` est un **entrepôt de type système de fichiers**, non une extension relationnelle du catalogue |
| A-004 | Table `Images(id INTEGER PRIMARY KEY, blob BLOB, sha256 BLOB)` ; SHA-256 **32 octets binaires** |
| A-005 | `Captures` contient **l'état courant** des annotations en JSON |
| A-006 | Une capture = une image ; aucune réutilisation partagée de BLOB entre fiches en V0 |
| A-007 | `INTEGER PRIMARY KEY` ; aucun GUID, aucune exigence de non-réutilisation d'ID supprimé |
| A-008 | Zéro ou une catégorie, plusieurs tags, miniatures de préférence avec la fiche |
| A-009 | **Aucun historique permanent**, seulement Annuler/Rétablir pendant l'édition |
| A-010 | Fichier(s) provisoire(s) local(aux), récupérables après plantage ; **suppression seulement après validation de Captures** ou abandon explicite |
| A-011 | Création : **image puis capture**. Suppression : **image puis capture** |
| A-012 | Vérification des références au démarrage, nettoyage **silencieux** des orphelins, avec priorité au provisoire correspondant |
| A-013 | **Aucun ATTACH**, aucune transaction interbase ou architecture distribuée imposés |
| A-014 | Exports et copies presse-papiers sortants sans suivi documentaire |
| A-015 | Aucun travail de développement déclenché par ces documents |

## 12. Détails réservés à la suite

### Document 004 — Intégrité, sauvegardes et cas limites

- Écriture fiable des fichiers de travail, y compris pendant une interruption.
- Récupération de l'image et, autant que possible, du JSON d'annotations et des champs déjà saisis.
- Suppression du provisoire après validation complète ou abandon volontaire.
- Cas d'un enregistrement interrompu après l'insertion BLOB mais avant la fiche.
- Comparaison SHA et résolution minimale des ambiguïtés.
- Vérification silencieuse des références ; purge des orphelins ; sauvegarde/restauration des deux bases et des provisoires en cours.
- Décision sur le traitement des écritures interrompues lorsqu'on édite une fiche **déjà archivée**.

### Document 005 — Exigences et validation

- Critères de bon fonctionnement des lectures catalogue sans chargement des BLOB originaux.
- Scénarios de plantage à chacun des points de l'enregistrement initial.
- Vérification du SHA binaire et de la correspondance avec le fichier provisoire.
- Tests de disparition de l'image ou de la fiche et de nettoyage silencieux.
- Tests de conservation / abandon des modifications selon le dialogue ergonomique.
- Tests de sauvegarde/restauration, de consultation et d'export.

**Conclusion :** un catalogue documentaire léger, un entrepôt d'images indépendant, un provisoire simple pour les incidents, et un contrôle d'ouverture. **Aucune centrale de la NASA.**
