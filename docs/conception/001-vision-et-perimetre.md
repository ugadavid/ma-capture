# 001 — Vision et périmètre fonctionnel

| Propriété | Valeur |
|---|---|
| Projet | **Ma Capture** — application Windows de capture d’écran, d’annotation et de classement |
| Nature | Document d’encadrement **fonctionnel**, préalable à toute mission de développement |
| Version du document | **0.1** |
| Date | **14 novembre 2026** |
| Statut | **Cadrage en cours** : décisions actées et questions ouvertes identifiées |
| Document suivant | 002 — Parcours utilisateurs et ergonomie *(à rédiger)* |

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

**[À ÉTUDIER]** La combinaison exacte des modes de capture en V0 (zone, fenêtre, écran entier), la manière de les déclencher et les raccourcis globaux. Ces modes constituent la cible fonctionnelle, mais le détail sera arrêté dans le document 002.

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

**[À ÉTUDIER]** Le retour visuel de confirmation et le comportement exact à l’ouverture d’une nouvelle capture lorsque l’édition courante n’a pas été enregistrée.

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

**[À ÉTUDIER]** L’éventuelle fonction de **copie sortante** de l’image annotée vers le presse-papiers ; elle ne doit pas être confondue avec le collage entrant d’images, explicitement exclu.

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
- Les annotations sont stockées **séparément**, avec leurs caractéristiques (type, position, dimensions, style, texte).
- L’affichage compose l’original et **une unique couche logique d’objets d’annotation**.
- Lors de l’export seulement, cette composition est rendue en PNG/JPEG.
- Aucun empilement complexe de calques, aucun système de masques, aucune ambition de logiciel de dessin professionnel.

Les coordonnées des objets devront être associées à l’image source plutôt qu’au seul zoom d’affichage.

**[À ÉTUDIER]** Annuler/Rétablir pendant une séance, manipulation exacte des poignées de redimensionnement, ordre d’affichage des annotations, et coût d’une éventuelle conservation de versions. La V0 ne présuppose **pas** un historique permanent de type Photoshop.

## 5. Bibliothèque et données

### 5.1 Distinction des responsabilités [VALIDÉ]

Le modèle fonctionnel distingue clairement :

1. **Contenu image** : pixels de la capture originale.
2. **Document de capture** : identité, titre, date, statut et données de classement.
3. **Annotations** : objets éditables associés à la capture.
4. **Données documentaires** : commentaires, catégories et tags, avec les relations adaptées.

Une même image originale pourra **éventuellement** servir à plusieurs documents ou variantes sans recopier son BLOB ; cette capacité a été jugée intéressante, mais **n’oblige pas** à construire un système de versions complet en V0.

### 5.2 Principes d’identification [VALIDÉ]

- Des **identifiants internes entiers** attribués automatiquement.
- **Aucun GUID/UUID** ajouté par principe.
- L’identifiant ne dépend pas du nom affiché.
- Le détail SQLite (`INTEGER PRIMARY KEY`, nécessité réelle ou non de `AUTOINCREMENT`) sera tranché dans le document 003, sans sur-ingénierie.

### 5.3 Isolation des images volumineuses [VALIDÉ]

Les images archivées seront conservées sous forme binaire **BLOB**, pas sous forme de chaînes Base64 ou hexadécimales.

La table dédiée au contenu binaire doit rester conceptuellement minimale : **`Images(id, blob)`**, point. Les métadonnées et relations résident dans d’autres tables. La recherche documentaire ne doit pas charger les images complètes inutilement.

**[À ÉTUDIER]** Une seule base SQLite contenant plusieurs tables, ou deux bases distinctes (catalogue et BLOB). La séparation physique n’est **pas** décidée. L’étude devra considérer performances réelles, intégrité transactionnelle, références entre bases, sauvegardes et simplicité de maintenance.

### 5.4 Gestion des captures non encore archivées [VALIDÉ / À ÉTUDIER]

**[VALIDÉ]** La capture en cours doit être récupérable après fermeture anormale : **ne pas perdre silencieusement l’image avant Enregistrer**.

**[À ÉTUDIER]** Son support technique : ligne persistante dans une table d’attente, ou **fichier de travail local externe**. Le fichier externe est une piste recevable s’il offre un avantage concret de simplicité, vitesse ou sûreté ; SQLite n’est pas une obligation pour chaque étape.

Dans l’hypothèse du fichier externe :
- un **nom fixe** peut être envisagé si une seule capture non archivée est autorisée à la fois ;
- sinon, il faut éviter tout écrasement d’une capture encore récupérable ;
- la transition vers la bibliothèque doit être vérifiée **avant** toute suppression du provisoire ;
- un redémarrage après incident doit proposer la **récupération** ;
- la conservation éventuelle des annotations en cours doit être précisée ;
- la politique de suppression lors d’une **fermeture normale** ou d’un abandon volontaire reste à expliciter.

Une seule capture éditée à la fois paraît une option simple pour V0, **sans décision ferme** sur la gestion de plusieurs brouillons.

## 6. Principes d’interface [VALIDÉ]

La V0 s’organise en **deux espaces**, sans multiplier les écrans :

1. **Éditeur / capture** : image annotable immédiatement à gauche ; barre d’outils ; panneau documentaire à droite ; **un seul bouton Enregistrer** avec raccourci.
2. **Bibliothèque** : retrouver les captures archivées, consulter leurs miniatures et informations, rechercher / classer, puis rouvrir la même vue d’édition.

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

## 9. Arbitrages restant ouverts

Les points suivants ne doivent pas être interprétés comme des commandes de réalisation :

| Réf. | Question | À traiter dans |
|---|---|---|
| Q-001 | Quels modes de capture et raccourcis exacts dès V0 ? | 002 / 005 |
| Q-002 | Que faire si une nouvelle capture est demandée alors qu’une capture non archivée existe ? | 002 / 004 |
| Q-003 | Politique exacte pour fermer, abandonner, récupérer et purger le provisoire ? | 002 / 004 |
| Q-004 | Stockage provisoire en base ou en fichier externe ? Une seule capture active ? | 003 / 004 |
| Q-005 | Une ou deux bases SQLite ? Quelles conséquences transactionnelles et de sauvegarde ? | 003 / 004 |
| Q-006 | Historique Annuler/Rétablir et versions persistantes : coût / intérêt / limites ? | 002 / 003 |
| Q-007 | Quels champs, relations catégorie/tag, miniatures et filtres pour la bibliothèque ? | 002 / 003 |
| Q-008 | Copie **sortante** vers le presse-papiers : incluse ou différée ? | 002 |
| Q-009 | Comment représenter et restaurer une édition interrompue, annotations incluses ? | 003 / 004 |
| Q-010 | Quelles vérifications définissent une V0 acceptable ? | 005 |

## 10. Suite du dossier de conception

Les documents prévus, **sans démarrage de développement à ce stade**, sont :

- **002 — Parcours utilisateurs et ergonomie** : écrans, interactions, outils, flux de capture en série.
- **003 — Architecture et modèle de données** : séparation BLOB, SQLite, identifiants, stockage provisoire, versionnage.
- **004 — Intégrité, sauvegardes et cas limites** : fermeture, plantage, récupération, corruption, transactions, restauration.
- **005 — Exigences et critères de validation** : exigences numérotées, recettes, définition précise de V0.

**Règle de gouvernance :** une discussion exploratoire n’est pas une décision de construire. Les éléments marqués **[À ÉTUDIER]** doivent être arbitrés explicitement avant d’être transformés en exigences ou en missions.
