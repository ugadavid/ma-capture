# 001 — Vision et périmètre fonctionnel

| Propriété | Valeur |
|---|---|
| Projet | **Ma Capture** — application Windows de capture d’écran, d’annotation et de classement |
| Nature | Document d’encadrement **fonctionnel**, préalable à toute mission de développement |
| Version du document | **0.3 — alignement avec le prototype graphique** |
| Date | **14 novembre 2026** |
| Statut | **Cadrage fonctionnel établi**, maquette HTML de référence disponible ; aucune mission Codex lancée |
| Document suivant | [002 — Parcours utilisateurs et ergonomie](002-parcours-utilisateurs-et-ergonomie.md) |

> **Convention de lecture** — **[VALIDÉ]** signifie explicitement retenu lors du cadrage ; **[À ÉTUDIER]** signifie qu’aucune solution définitive n’a été arrêtée ; **[HORS V0]** désigne une fonction volontairement écartée de la première version. Ce document n’autorise **aucune implémentation** ni mission Codex à ce stade.

## 1. Vision

**Ma Capture** est un outil personnel Windows pour **réaliser une capture d’écran, y faire ressortir des éléments et la retrouver dans une bibliothèque locale**.

L’ambition n’est ni de refaire Photoshop, ni de développer un gestionnaire universel d’images, ni de reproduire intégralement l’outil Capture d’écran de Windows. Le produit doit combiner trois qualités :

1. **Immédiateté** — capturer et enregistrer en quelques gestes, sans obligation de renseigner une fiche.
2. **Édition directe** — tracer sur l’image des indications simples, visibles et modifiables, sans modifier l’original.
3. **Mémoire documentaire** — retrouver, compléter et reclasser les captures produites par l’application.

**Principe directeur [VALIDÉ] :** *capturer maintenant, documenter maintenant ou plus tard*. La documentation enrichit le travail ; elle ne doit jamais empêcher une capture rapide.

## 2. Frontières du produit

### 2.1 Ce qui entre dans le périmètre [VALIDÉ]

- Les captures **réalisées par Ma Capture elle-même**.
- Leur présentation immédiate dans l’éditeur.
- Des annotations graphiques simples permettant de **désigner des zones** et d’ajouter du texte.
- Des informations documentaires facultatives, saisissables lors de la capture ou ultérieurement.
- Leur conservation dans une bibliothèque locale.
- **Favoris V0** : marquer/démarquer une capture par étoile et retrouver les favoris en mosaïque ou liste.
- Leur export manuel en image PNG ou JPEG.

### 2.2 Ce qui n’entre pas dans le périmètre [VALIDÉ]

- **Aucun import** d’images existantes depuis le disque.
- **Aucun collage entrant** d’une image issue du presse-papiers.
- Aucune exploration, indexation ou surveillance des dossiers de l’utilisateur pour découvrir des images.
- Aucun suivi des fichiers produits après export : **l’application exporte puis les oublie**.
- Aucune connexion obligatoire, aucun serveur ni système documentaire distant.
- Aucun gestionnaire de multiples calques, masque avancé ou suite de retouche photographique.

La bibliothèque interne ne contient donc **que des captures créées avec Ma Capture**. « Local » désigne l’exécution et la conservation sur le poste, et non une autorisation de parcourir arbitrairement ses fichiers.

### 2.3 Hors V0 [VALIDÉ]

- Gestion **avancée** de plusieurs moniteurs.
- Capture ou montage vidéo.
- OCR et reconnaissance automatique du contenu.
- Retouche photo avancée : filtres, colorimétrie, effets, masques.
- Synchronisation cloud et collaboration multiutilisateur.

D’autres fonctions seront volontairement exclues si elles compromettent la simplicité ; elles devront être justifiées avant d’être ajoutées au périmètre.

## 3. Parcours utilisateur de référence

### 3.1 Capturer

1. L’utilisateur déclenche une capture à partir de l’application.
2. L’image est affichée **immédiatement dans la zone principale**, à gauche.
3. Une fiche documentaire apparaît **simultanément à droite**, avec notamment un **titre proposé par défaut**.
4. L’utilisateur peut commencer aussitôt à annoter l’image ou renseigner la fiche, **dans le même écran**.

**[VALIDÉ]** La capture doit être placée sans délai dans un **espace de récupération persistant**, afin de ne pas disparaître en cas de plantage avant son enregistrement définitif. Il ne s’agit **pas** d’une table SQLite `TEMP`, qui disparaîtrait avec sa connexion.

**[VALIDÉ]** Les trois modes de V0 sont **zone rectangulaire, fenêtre et écran entier**, avec **zone rectangulaire présélectionnée au lancement**. Le raccourci global de V0 est **`Ctrl + Alt + C`**, distinct de l'outil Windows ; sa personnalisation est reportée en V2.

### 3.2 Annoter et documenter, sans étape supplémentaire

L’utilisateur travaille depuis **un seul écran d’édition** :

- **À gauche :** image capturée, directement annotable et modifiable.
- **Au-dessus / à proximité :** outils d’annotation, couleurs, épaisseurs et commandes d’édition.
- **À droite :** titre, description, commentaires, catégorie(s), étiquettes/tags et autres renseignements documentaires pertinents.

**[VALIDÉ]** Tous les champs documentaires ajoutés par l’utilisateur sont **facultatifs**. Il n’a pas à choisir entre un « mode rapide » et un « mode avancé » : il remplit ce qui l’intéresse, ou rien du tout.

**[VALIDÉ]** Le titre est prérempli avec une proposition lisible, de type `Capture 2026-11-14 21-45-03`. Un identifiant interne entier assure l’identité du document, indépendamment de son titre. Un changement de titre ne change jamais cet identifiant.

### 3.3 Enregistrer

**[VALIDÉ]** Une seule action **Enregistrer**, accessible par un bouton et un raccourci clavier (cible : `Ctrl + S`), conserve la capture, ses annotations et les informations documentaires éventuellement renseignées.

- Si aucun champ n’a été modifié, le **titre par défaut** suffit : l’enregistrement réussit sans autre saisie.
- Si des renseignements ont été ajoutés, ils sont enregistrés **dans la même opération logique**.
- Une fois l’enregistrement achevé, l’utilisateur peut immédiatement réaliser une nouvelle capture.
- **Pas** de second bouton imposé « Enregistrer et capturer à nouveau ».
- **Pas** de boîte de dialogue documentaire obligatoire avant la sauvegarde rapide.

Les prises de vues successives doivent donc être fluides : *capturer → éventuellement annoter / renseigner → Enregistrer → capturer à nouveau*.

**[VALIDÉ]** En cas de travail non enregistré, la nouvelle capture, la navigation ou la fermeture sont protégées par **Enregistrer / Ne pas enregistrer / Annuler** ; Annuler conserve le travail courant et renonce à l'action demandée. La sauvegarde normale donne une confirmation discrète.

### 3.4 Revenir plus tard

**[VALIDÉ]** Une capture enregistrée peut être retrouvée dans la bibliothèque, puis **rouverte dans le même environnement d’édition**. L’utilisateur peut alors :

- renommer son titre ;
- compléter ou modifier les informations, commentaires, catégories et tags ;
- reprendre, déplacer, recolorer ou supprimer les annotations conservées séparément de l’image originale.

L’absence de classement immédiat ne bloque jamais l’enregistrement.

**[À ÉTUDIER]** Un filtre « À classer », la définition d’une capture « classée », les opérations de classement en masse et la richesse exacte de la recherche.

### 3.5 Exporter

**[VALIDÉ]** Un export produit, à la demande, une image standard **PNG ou JPEG**, réunissant visuellement la capture et ses annotations. L’image source conservée dans l’application reste intacte.

**[VALIDÉ]** Après export, **aucune traçabilité** n’est conservée sur le fichier exporté : pas de table d’historique des exports, pas de mémorisation de son chemin comme dépendance, pas de vérification de son existence, pas de tentative de le retrouver ou de le resynchroniser. Ce fichier est une **copie indépendante**.

**[VALIDÉ]** La **copie sortante** de l'image annotée vers le presse-papiers est prévue. Export et copie rendent **l'état visible, même non enregistré**, sans archiver automatiquement ; aucun collage entrant d'images n'est autorisé.

## 4. Modèle d’annotation

### 4.1 Philosophie [VALIDÉ]

La fonction principale est de **désigner et mettre en évidence des zones**, pas de les masquer. Les formes sont des **contours** dans une couleur choisie (rouge, bleu, etc.), **sans remplissage opaque**.

Outils de base souhaités :
- ligne / trait ;
- flèche ;
- rectangle à contour ;
- cercle / ellipse à contour ;
- texte libre ou commentaire visible sur l’image ;
- sélection, déplacement, modification et suppression des objets.

L’épaisseur et la couleur du tracé doivent être réglables. L’ergonomie précise de la palette relève du document 002.

### 4.2 Une seule couche logique [VALIDÉ]

- La capture originale est **préservée**.
- Les annotations sont **indépendantes de l'image originale** et conservées en **JSON dans la fiche Captures**, avec leurs caractéristiques (type, position, dimensions, style, texte).
- L’affichage compose l’original et **une unique couche logique d’objets d’annotation**.
- Lors de l’export seulement, cette composition est rendue en PNG/JPEG.
- Aucun empilement complexe de calques, aucun système de masques, aucune ambition de logiciel de dessin professionnel.

Les coordonnées des objets devront être associées à l’image source plutôt qu’au seul zoom d’affichage.

**[VALIDÉ]** Annuler/Rétablir fonctionne **pendant la séance seulement**. **Aucun versionnage permanent** en V0. Les poignées de redimensionnement et l'ordre d'affichage exact restent des détails d'ergonomie à affiner.

## 5. Bibliothèque et données

### 5.1 Distinction des responsabilités [VALIDÉ]

Le modèle fonctionnel distingue clairement :

1. **Contenu image** : pixels de la capture originale.
2. **Document de capture** : identité, titre, date, statut et données de classement.
3. **Annotations** : objets éditables associés à la capture.
4. **Données documentaires** : commentaires, catégories et tags, avec les relations adaptées.

**[VALIDÉ] Une capture correspond à une image, sans partage de BLOB entre captures en V0.** Les annotations courantes sont enregistrées au format JSON **dans la fiche Captures** ; la couche visuelle reste indépendante de l'image originale.

### 5.2 Principes d’identification [VALIDÉ]

- Des **identifiants internes entiers** attribués automatiquement.
- **Aucun GUID/UUID** ajouté par principe.
- L’identifiant ne dépend pas du nom affiché.
- Le modèle retient **`INTEGER PRIMARY KEY`**, sans GUID ni nécessité de non-réutilisation d'un ID effacé.

### 5.3 Isolation des images volumineuses [VALIDÉ]

Les images archivées seront conservées sous forme binaire **BLOB**, pas sous forme de chaînes Base64 ou hexadécimales.

**[VALIDÉ]** Deux fichiers SQLite : **`catalogue.db`** (modèle métier) et **`images.db`** (entrepôt binaire, comparable à un dossier d'images). Sa table **`Images(id, blob, sha256)`** ne contient que l'ID, le contenu binaire et son SHA-256 de **32 octets**. Aucun lien SQL interbase imposé ; les recherches documentaires ne chargent pas les originaux.

### 5.4 Gestion des captures non encore archivées [VALIDÉ]

La capture non enregistrée est protégée par un **PNG provisoire local** et un **JSON de travail**. Une seule capture active à la fois : pas de capture en rafale ni d'écrasement silencieux. Pour une capture archivée en cours de modification, le JSON provisoire suffit ; son BLOB original existe déjà.

Après l'insertion du BLOB et de son SHA dans `images.db`, la fiche est insérée dans `catalogue.db`. **Le provisoire n'est supprimé qu'après succès de la fiche**, ou après abandon explicite. Au redémarrage, l'application propose la récupération du travail et un **contrôle des ID** nettoie silencieusement les orphelins non liés à ce provisoire.

La **sauvegarde/restauration manuelle en ZIP des deux bases** est définie dans le document 004. Les détails d'écriture sûre du JSON sont précisés au même endroit.

## 6. Principes d’interface [VALIDÉ]

La V0 s’organise autour du **splash de vérification**, d'un **petit bandeau de capture**, puis de **deux espaces de travail**, sans multiplier les écrans :

1. **Éditeur / capture** : image annotable immédiatement à gauche ; barre d’outils ; panneau documentaire à droite ; **un seul bouton Enregistrer** avec raccourci.
2. **Bibliothèque** : retrouver les captures archivées, consulter leurs miniatures et informations, rechercher / classer, puis rouvrir la même vue d’édition.

Le **guide graphique** et le [prototype HTML validé](../graphisme/ma-capture-prototype.html) sont les références d'interface, complétés par les six PNG de `docs/graphisme/`. Les popups applicatifs suivent la capture `03-editeur-popup.png`.

Le panneau documentaire doit pouvoir être utilisé immédiatement, mais **jamais requis** pour enregistrer. Les adaptations de largeur, le zoom, les filtres et la navigation précise relèvent du document 002.

## 7. Qualités et contraintes transversales

**[VALIDÉ]**
- **Local d’abord** : données locales, fonctionnement sans service distant obligatoire.
- **Résilience** : capture provisoire récupérable après interruption et écriture définitive contrôlée.
- **Intégrité** : modèle relationnel clair, sauvegarde/restauration et contrôle de cohérence prévus.
- **Non-destruction** : l’image originale reste intacte lors des annotations et des exports.
- **Simplicité** : pas de procédure imposée pour nommer, classer ou commenter avant d’enregistrer.
- **Sobriété** : éviter les fonctions annexes, surveillances de disque, doublons de BLOB ou mécanismes de versions coûteux sans bénéfice démontré.

Les stratégies de sauvegarde, de migration, de réparation et les scénarios de panne appartiennent au document 004. Les critères mesurables appartiennent au document 005.

## 8. Décisions fermes récapitulatives

| Réf. | Décision |
|---|---|
| D-001 | Application dédiée aux **seules captures créées en son sein** ; pas d’import ni de collage entrant. |
| D-002 | Capture non encore enregistrée **protégée par une conservation provisoire récupérable**. |
| D-003 | Édition de l’image **dès l’écran de capture** ; panneau de métadonnées simultanément visible à droite. |
| D-004 | **Un seul bouton Enregistrer** et un raccourci ; titre automatique ; métadonnées facultatives. |
| D-005 | Informations documentaires modifiables **ultérieurement** depuis la bibliothèque. |
| D-006 | Annotations simples, contours colorés **sans remplissage opaque**, texte, original intact et une couche logique. |
| D-007 | Identifiants **entiers** ; pas de GUID/UUID ; BLOB binaires isolés des tables documentaires. |
| D-008 | Données et fonctionnement **locaux** ; pas de scan de fichiers externes. |
| D-009 | Export **PNG/JPEG**, puis **oubli complet** du fichier exporté. |
| D-010 | Deux espaces : **éditeur** et **bibliothèque** ; pas d’application de retouche multi-calques. |
| D-011 | Multimoniteur avancé et fonctionnalités lourdes **hors V0**. |
| D-012 | **Pas de mission Codex** tant que le dossier de conception n’est pas suffisamment défini et validé. |
| D-013 | Favoris par étoile, filtre dédié en mosaïque et liste ; pas de sélection multiple en V0. |
| D-014 | Raccourci global `Ctrl + Alt + C` en V0 ; personnalisation en V2. |
| D-015 | Guide HTML de référence, PNG cohérents et popups applicatifs harmonisés graphiquement. |

## 9. Points encore ouverts après consolidation

Les anciennes questions Q-001 à Q-010 ont été **majoritairement arbitrées** par les documents 002 à 005 : modes de capture, sauvegarde, récupération, architecture à deux bases, annotations, versionnage, copie sortante et critères de recette.

Les **détails encore réellement ouverts** sont consignés dans le [registre de relecture transversale](relecture-transversale-et-points-ouverts.md). Ils ne doivent pas être transformés silencieusement en fonctionnalités.

## 10. Dossier de conception consolidé

- [002 — Parcours utilisateurs et ergonomie](002-parcours-utilisateurs-et-ergonomie.md)
- [003 — Architecture et modèle de données](003-architecture-et-modele-de-donnees.md)
- [004 — Intégrité, sauvegardes et cas limites](004-integrite-sauvegardes-et-cas-limites.md)
- [005 — Exigences et critères de validation](005-exigences-et-criteres-de-validation.md)

**Aucune mission Codex n'est déclenchée par cette consolidation.**

**Règle de gouvernance :** une discussion exploratoire n’est pas une décision de construire. Les éléments marqués **[À ÉTUDIER]** doivent être arbitrés explicitement avant d’être transformés en exigences ou en missions.
