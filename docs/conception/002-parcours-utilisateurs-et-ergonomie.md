# 002 — Parcours utilisateurs et ergonomie

| Propriété | Valeur |
|---|---|
| Projet | **Ma Capture** |
| Nature | Document de conception fonctionnelle et ergonomique |
| Version du document | **0.1** |
| Date | **14 novembre 2026** |
| Statut | **Cadrage** — décisions validées, précisions techniques différées |
| Document précédent | [001 — Vision et périmètre fonctionnel](001-vision-et-perimetre.md) |
| Documents suivants | 003 Architecture et données ; 004 Intégrité et récupération ; 005 Exigences et recette |

> **Important :** ce document décrit des **comportements attendus**, pas une mission de développement. Les décisions mentionnées **[VALIDÉ]** proviennent du cadrage. Les détails marqués **[À PRÉCISER]** restent ouverts. Aucun code ne doit être développé sur cette seule base.

## 1. Intentions ergonomiques

1. **Capturer, annoter, enregistrer** sans dialogue documentaire obligatoire.
2. Permettre une **annotation immédiate sur l’image**, sans deuxième écran ni bascule vers un mode « édition avancée ».
3. Rendre la **documentation facultative mais disponible immédiatement**, dans un panneau à droite.
4. Retrouver une capture et pouvoir la modifier **plus tard**, dans le même environnement.
5. **Ne jamais perdre silencieusement** une capture ou des modifications non enregistrées.
6. Éviter les mécanismes d’édition photo ou de capture en rafale qui compliqueraient le flux.
7. Garder le produit limité aux **captures créées par Ma Capture** : aucun import d’image et aucun collage entrant depuis le presse-papiers.

Le principe clé est : **capturer maintenant ; documenter maintenant ou plus tard**.

## 2. Navigation générale [VALIDÉ]

L’application comporte **deux espaces principaux** :

- **Éditeur / capture** : création, visualisation, annotation, documentation et enregistrement.
- **Bibliothèque** : consultation des captures archivées, recherche, filtres, reclassement et ouverture dans l’éditeur.

La navigation doit rester évidente, sans multiplication de fenêtres. Le panneau documentaire reste intégré à l’éditeur. Les dialogues modaux sont réservés aux actions qui le justifient, principalement celles pouvant entraîner la perte d’un travail non enregistré.

### 2.1 Schéma de disposition — éditeur

Représentation indicative, **non dimensionnée** :

```text
┌───────────────────────────────────────────────────────────────────────────┐
│ Ma Capture            [Capturer ▾]   [Bibliothèque]       [Enregistrer]  │
├────────────────────────────────────────────────┬──────────────────────────┤
│ [Sélection] [Trait] [Flèche] [□] [○] [Texte]  │ Informations             │
│ [Couleur] [Épaisseur] [Annuler] [Rétablir]     │                          │
│                                                │ Titre : [automatique…]  │
│                                                │ Date/heure : …           │
│             IMAGE CAPTURÉE                    │ Catégorie : […]         │
│          ANNOTABLE IMMÉDIATEMENT              │ Tags : […]              │
│                                                │ Description : […]       │
│                                                │ Commentaire : […]       │
│                                                │                          │
├────────────────────────────────────────────────┴──────────────────────────┤
│ Zoom : [Adapter] [100 %]                       État : non enregistré    │
└───────────────────────────────────────────────────────────────────────────┘
```

**[VALIDÉ]** L’image occupe l’espace principal **à gauche**. La fiche textuelle est **à droite**. La barre d’outils d’annotation est immédiatement disponible : les formes se dessinent directement sur la capture affichée.

**[VALIDÉ]** Le panneau documentaire est redimensionnable, afin de laisser davantage de place à l’image si nécessaire.

**[À PRÉCISER]** Dimensions, couleurs, icônes, emplacement exact de chaque commande, thème graphique et comportement des très petites fenêtres. Ce schéma n’est pas une maquette visuelle définitive.

## 3. Parcours P-01 — Réaliser une capture

### 3.1 Modes de capture [VALIDÉ]

La V0 propose :

1. **Zone rectangulaire** sélectionnée par l’utilisateur ;
2. **Fenêtre** sélectionnée ;
3. **Écran entier**.

L’utilisateur choisit le mode depuis une commande de capture simple. La **gestion multimoniteur avancée** n’est pas une priorité de V0.

**[À PRÉCISER]** Le mode sélectionné par défaut, l’éventuelle mémorisation du dernier mode, les détails du survol des fenêtres et les raccourcis de déclenchement globaux. Aucun mécanisme de capture en rafale n’est demandé.

### 3.2 Arrivée dans l’éditeur [VALIDÉ]

Une fois la prise de vue effectuée :

- la capture s’affiche **immédiatement** à gauche ;
- les outils de dessin sont utilisables **tout de suite** ;
- le panneau de droite présente une **fiche facultative** déjà prête ;
- le **titre** est prérempli automatiquement (exemple : « Capture 2026-11-14 22-20-22 ») ;
- la date et l’heure de la capture sont renseignées automatiquement ;
- aucune fenêtre ne demande de nom ou de dossier avant de pouvoir travailler.

La capture non encore archivée doit être protégée par un **stockage provisoire récupérable**. Sa matérialisation exacte est du ressort des documents 003 et 004. Ce stockage n’est pas un enregistrement définitif dans la bibliothèque.

### 3.3 Capture annulée lors de la sélection

Si l’utilisateur abandonne la sélection de zone ou de fenêtre **avant de produire une image**, aucune nouvelle capture n’est ajoutée. L’écran précédent reste disponible. Aucune fiche documentaire vide ne doit être créée.

## 4. Parcours P-02 — Annoter l’image

### 4.1 Outils [VALIDÉ]

La V0 comporte une barre d’outils courte :

| Outil | Geste attendu |
|---|---|
| Sélection | Sélectionner un objet existant pour le déplacer, le modifier ou le supprimer |
| Trait / ligne | Tracer un segment ou trait de repérage |
| Flèche | Tracer une flèche de mise en évidence |
| Rectangle | Encadrer une zone **sans remplissage** |
| Cercle / ellipse | Entourer une zone **sans remplissage** |
| Texte | Placer un texte libre sur l’image |
| Couleur | Choisir la couleur des objets graphiques |
| Épaisseur | Régler l’épaisseur des traits et contours |
| Annuler / Rétablir | Revenir sur les actions de la séance d’édition |

**[VALIDÉ]** Les formes servent à **montrer**, non à cacher. Un rectangle rouge signifie un **contour rouge avec intérieur transparent**, pas un aplat opaque. Même principe pour l’ellipse.

Les textes placés sur l’image sont des **annotations graphiques** ; ils sont distincts des descriptions et commentaires documentaires saisis dans le panneau de droite.

### 4.2 Manipulation des objets [VALIDÉ]

- Cliquer sur un outil, puis tracer directement sur l’image.
- Choisir un objet en mode **Sélection**.
- Déplacer ou redimensionner cet objet.
- Changer sa couleur ou son épaisseur sans recréer l’annotation.
- Modifier un texte existant, notamment par double-clic.
- Supprimer l’objet sélectionné à l’aide de la touche **Suppr**.
- Utiliser **Ctrl + Z** pour Annuler et **Ctrl + Y** pour Rétablir pendant la séance.

Une capture conservée dans la bibliothèque, rouverte ultérieurement, présente des annotations **toujours modifiables**. Il n’y a qu’**une couche logique d’annotations**, sans panneau de calques ou moteur de retouche avancée.

**[À PRÉCISER]** Gestion des poignées, ordre visuel d’annotations qui se recouvrent, style exact des textes, limites de l’historique Annuler/Rétablir et éventuel versionnage persistant. Le coût d’un historique permanent relève du document 003.

### 4.3 Zoom et navigation [VALIDÉ]

- **Adapter à la fenêtre** ;
- **100 % / taille réelle** ;
- **zoom à la molette**.

Le zoom n’altère ni l’image ni les positions des annotations : leurs coordonnées sont liées à la capture originale. Aucun outil de rotation, de filtre ou de transformation photographique avancée n’est attendu en V0.

## 5. Parcours P-03 — Renseigner la fiche documentaire

### 5.1 Champs de l’écran [VALIDÉ]

| Champ | Comportement |
|---|---|
| Titre | **Prérempli automatiquement**, librement modifiable avant et après enregistrement |
| Date et heure de capture | Renseignées automatiquement, **en lecture seule** |
| Description | Texte facultatif |
| Commentaire documentaire | Texte facultatif, **non dessiné sur l’image** |
| Catégorie | **Zéro ou une** catégorie par capture ; choix facultatif |
| Tags / étiquettes | **Zéro, un ou plusieurs** tags |

La seule donnée visible nécessaire à un enregistrement rapide est le **titre par défaut**, déjà présent. **Aucun de ces champs ne doit bloquer Enregistrer** lorsque l’utilisateur ne les modifie pas.

**[VALIDÉ]** Une capture n’a **qu’une catégorie au maximum**, mais peut porter **plusieurs tags**. Il n’y a pas de hiérarchie documentaire complexe demandée en V0.

**[À PRÉCISER]** Création et renommage de catégories/tags depuis la fiche ou depuis un espace de gestion ; validations de longueur et de doublons ; éventuels autres champs justifiés par l’usage.

### 5.2 Pas de modes imposés [VALIDÉ]

Un utilisateur peut :
- ne remplir **aucun** champ et enregistrer immédiatement ;
- annoter l’image puis enregistrer, sans documentation ;
- renseigner le titre et les textes avant de sauvegarder ;
- conserver la capture avec son titre par défaut, puis **revenir plus tard** la classer ou la commenter.

Il n’existe **ni mode rapide**, ni mode avancé à sélectionner, ni étape obligatoire « classer avant de sauvegarder ».

## 6. Parcours P-04 — Enregistrer

### 6.1 Commande unique [VALIDÉ]

**Un seul bouton « Enregistrer »**, accompagné du raccourci **Ctrl + S**.

- Pour une nouvelle capture : l’action archive l’image originale, ses annotations et la fiche dans la bibliothèque.
- Pour une capture déjà archivée : elle conserve ses modifications.
- Aucun second bouton « Enregistrer et nouvelle capture ».
- Aucune demande de répertoire ni dialogue documentaire bloquant lors de la sauvegarde normale.
- Après succès, une confirmation **discrète** (par exemple « Enregistré » dans la barre d’état) suffit.

Une fois une capture enregistrée, l’utilisateur peut réaliser une **nouvelle capture** directement.

### 6.2 États de travail

Pour raisonner sur les dialogues sans imposer un schéma physique :

- **Nouvelle capture non enregistrée** : l’image existe en provisoire, mais pas encore comme document archivé.
- **Document archivé sans modifications** : quitter ou naviguer ne risque pas de perdre d’édition courante.
- **Document archivé avec modifications non enregistrées** : annotations et/ou fiche ont été changées depuis le dernier enregistrement.

**[VALIDÉ]** L’identité interne d’un document est un **ID entier attribué automatiquement**, indépendant du titre. Il n’y a aucune raison d’exposer un GUID à l’utilisateur.

## 7. Parcours P-05 — Quitter ou naviguer avec un travail non enregistré

### 7.1 Le dialogue à trois choix [VALIDÉ — COMPORTEMENT CENTRAL]

Lorsqu’une action risquerait de faire perdre une **nouvelle capture non enregistrée** ou des **modifications non enregistrées**, afficher l’image dans l’éditeur et le dialogue :

```text
                 Modifications non enregistrées

     [ Enregistrer ]  [ Ne pas enregistrer ]  [ Annuler ]
```

Les trois boutons doivent être **distincts**, comme dans l’exemple d’interface fourni lors du cadrage.

| Choix | Effet |
|---|---|
| **Enregistrer** | Enregistre le travail ; **si et seulement si l’enregistrement réussit**, poursuit l’action initialement demandée |
| **Ne pas enregistrer** | Abandonne explicitement le travail non sauvegardé, puis poursuit l’action initialement demandée |
| **Annuler** | **Annule l’action initialement demandée** et laisse l’utilisateur sur la capture actuelle, sans effacer son travail |

Ce trio évite l’ambiguïté d’un simple « Enregistrer / Annuler » : **Annuler ne signifie jamais supprimer une capture**.

### 7.2 Déclencheurs

Le dialogue est utilisé, si nécessaire, pour :
- fermer l’application ;
- demander une **nouvelle capture** alors que l’actuelle n’est pas sauvegardée ;
- ouvrir une autre capture depuis la bibliothèque ;
- naviguer vers la bibliothèque si cela abandonnerait l’édition courante ;
- toute autre action susceptible de détruire un travail non enregistré.

**Précision [VALIDÉ] :** lors d’une demande de nouvelle capture, le bouton **Annuler** annule **la nouvelle capture**, pas le travail affiché. L’utilisateur peut simplement cliquer sur Enregistrer puis relancer une capture. Il n’est pas question de développer un système de capture en rafale.

Si aucun travail non enregistré n’existe, aucune fenêtre de confirmation n’est nécessaire.

### 7.3 Sorties en erreur [VALIDÉ]

Si l’utilisateur clique **Enregistrer** mais que l’écriture échoue, l’application **ne poursuit pas** l’action qui aurait abandonné l’édition. Le travail reste présent et récupérable ; un message d’erreur explicite doit permettre de comprendre le blocage.

**[À PRÉCISER]** Le détail des échecs, l’emplacement du stockage de secours et les scénarios d’interruption pendant l’enregistrement (document 004).

## 8. Parcours P-06 — Récupérer après plantage

**[VALIDÉ]** Après un arrêt anormal, l’application doit pouvoir présenter la **capture retrouvée dans l’éditeur**, image visible, avec un dialogue cohérent avec la règle à trois boutons :

- **Enregistrer** : archiver la capture récupérée, puis continuer normalement.
- **Ne pas enregistrer** : abandonner **explicitement** la récupération et le provisoire correspondant.
- **Annuler** : fermer la proposition, **rester sur la capture récupérée**, et permettre de décider plus tard ; ne pas effacer le provisoire.

Cette interprétation maintient la sémantique « Annuler = renoncer à l’action demandée, sans perte de données ». L’image doit être **visible** au moment de cette proposition, pas cachée derrière un écran de démarrage vide.

**[À PRÉCISER]** La restitution d’annotations et de champs documentaires saisis avant le plantage, la fréquence de persistance provisoire et la suppression sûre des brouillons (documents 003 et 004). La récupération de **l’image** est non négociable ; l’étendue de récupération du travail supplémentaire sera spécifiée et testée.

## 9. Parcours P-07 — Retrouver et modifier une capture

### 9.1 Bibliothèque [VALIDÉ]

Deux présentations de la **même collection locale** :

- **Mosaïque** : miniatures de captures, titre et informations essentielles ;
- **Liste** : captures sous forme tabulaire, avec colonnes utiles (titre, date, catégorie notamment).

L’utilisateur peut :
- rechercher dans les **titres, descriptions et commentaires documentaires** ;
- rechercher/filtrer par **catégorie** et **tags** ;
- trier par **date** ou **titre** ;
- ouvrir une capture pour édition (par exemple par double-clic) ;
- compléter ou modifier ses informations et annotations.

L’édition d’une capture archivée rouvre **le même écran**, sans second éditeur spécialisé.

**[À PRÉCISER]** Colonnes définitives, recherche multi-termes, miniatures, comportement des filtres multiples, choix de vue mémorisé ou non, suppression d’une capture, et existence éventuelle d’un filtre « À classer ». Ces détails ne doivent pas provoquer la création d’un gestionnaire universel d’images.

### 9.2 Travail en cours lors de la navigation

Toute modification non enregistrée depuis la bibliothèque ou l’éditeur suit **exactement** le dialogue de la section 7. Aucun abandon silencieux.

## 10. Parcours P-08 — Produire une image destinée à l’extérieur

### 10.1 Export de fichier [VALIDÉ]

Une commande **Exporter** propose un fichier image **PNG ou JPEG** et son emplacement au moyen du dialogue standard Windows.

- L’image exportée contient l’original **et les annotations visibles**.
- L’original interne et les objets d’annotation restent **intacts et éditables**.
- Une fois l’export réalisé, **aucun historique des exports n’est conservé**.
- L’application ne recherche pas le fichier, ne surveille pas son dossier, ne gère ni son déplacement ni sa suppression.

### 10.2 Copie sortante [VALIDÉ]

Une commande **Copier l’image annotée** place le rendu dans le presse-papiers afin de le coller dans un autre logiciel.

**Attention :** cette fonction **sortante** ne change rien à l’interdiction d’**importer ou coller une image entrante** dans Ma Capture.

### 10.3 Travaux non enregistrés et export

**[À PRÉCISER]** Si la capture ou ses annotations ont des modifications non enregistrées, décider si « Exporter / Copier » rend l’état visible courant ou demande d’enregistrer d’abord. Dans tous les cas, **Exporter ne doit pas être confondu avec Enregistrer dans la bibliothèque**.

## 11. Tableau des raccourcis proposés

| Action | Raccourci | Statut |
|---|---|---|
| Enregistrer | **Ctrl + S** | **[VALIDÉ]** |
| Annuler une édition | **Ctrl + Z** | **[VALIDÉ]** |
| Rétablir une édition | **Ctrl + Y** | **[VALIDÉ]** |
| Supprimer l’annotation sélectionnée | **Suppr** | **[VALIDÉ]** |
| Zoom | **Molette** | **[VALIDÉ]** |
| Capturer (raccourci global) | Non défini | **[À PRÉCISER]** |
| Exporter / Copier | Non définis | **[À PRÉCISER]** |

La gestion du **focus clavier** est essentielle : notamment, la touche Suppr ne doit jamais effacer une capture lorsque le curseur est dans un champ texte ; Ctrl + Z doit être cohérent selon que l’on édite un champ ou un objet graphique.

## 12. Scénarios ergonomiques de référence

| Réf. | Scénario | Résultat attendu |
|---|---|---|
| UX-01 | Capturer une zone, ne saisir aucun renseignement, cliquer Enregistrer | Capture archivée avec titre automatique |
| UX-02 | Capturer, encadrer un bouton en rouge, ajouter un texte, Enregistrer | Image source intacte, annotations et texte conservés |
| UX-03 | Capturer, remplir description et tags, puis Enregistrer | Tous les renseignements conservés, catégorie facultative |
| UX-04 | Ouvrir une ancienne capture, changer son titre et déplacer une flèche, puis Enregistrer | Changements conservés ; même ID interne |
| UX-05 | Nouvelle capture non enregistrée puis demande de nouvelle capture ; cliquer Annuler | **Aucune nouvelle capture produite**, travail courant intact |
| UX-06 | Sur ce même dialogue, cliquer Ne pas enregistrer | Abandon explicite du provisoire, puis nouvelle capture possible |
| UX-07 | Modifications non sauvegardées puis fermeture ; cliquer Enregistrer | Enregistrement réussi avant fermeture |
| UX-08 | Même situation ; échec de l’enregistrement | Ne pas fermer ni abandonner le travail |
| UX-09 | Interruption anormale, puis redémarrage | Image récupérée visible et choix Enregistrer / Ne pas enregistrer / Annuler |
| UX-10 | Récupération après plantage ; cliquer Annuler | Rester sur l’image récupérée, sans suppression |
| UX-11 | Rechercher par titre/tag et basculer mosaïque → liste | Même bibliothèque ; résultat accessible dans les deux vues |
| UX-12 | Exporter un PNG annoté | Image aplatie disponible ; aucun suivi du fichier par l’application |
| UX-13 | Copier l’image annotée | Rendu disponible dans le presse-papiers ; aucune entrée créée par collage dans l’application |
| UX-14 | Modifier le zoom d’une capture annotée | Contours et textes restent positionnés sur leurs zones |

Les conditions de test précises, erreurs et critères quantifiables seront développés dans le **document 005**.

## 13. Décisions récapitulatives de ce document

| Réf. | Décision |
|---|---|
| U-001 | Deux espaces : éditeur et bibliothèque |
| U-002 | Trois modes : zone, fenêtre, écran entier |
| U-003 | Image directement éditable à gauche, outils immédiats, panneau documentaire à droite |
| U-004 | Formes à contour coloré **sans remplissage**, plus textes et édition d’objets |
| U-005 | Zoom molette, adapter, 100 % ; Annuler/Rétablir et Suppression |
| U-006 | Titre automatique ; description/commentaire facultatifs ; **une catégorie au plus** ; plusieurs tags |
| U-007 | Un seul Enregistrer, accessible par **Ctrl + S** |
| U-008 | En cas de risque de perte : **Enregistrer / Ne pas enregistrer / Annuler** avec leur sémantique distincte |
| U-009 | Après plantage, image récupérée visible et choix non destructif |
| U-010 | Bibliothèque en mosaïque **et** liste, recherche, tri et filtres |
| U-011 | Export PNG/JPEG sans suivi ; copie **sortante** de l’image annotée |
| U-012 | Pas d’import, de collage entrant, de scan du disque ni de capture en rafale |

## 14. Questions transmises aux documents suivants

- **003 — Architecture et données :** stockage provisoire fichier/base ; une ou deux bases SQLite ; modèle des annotations ; stratégie d’ID ; miniatures ; coût des versions ; persistance d’Annuler/Rétablir.
- **004 — Intégrité et récupération :** moment et sûreté de la sauvegarde provisoire ; garantie sur les annotations/documentation après plantage ; élimination du provisoire après réussite ou abandon explicite ; fermeture normale ; erreurs d’écriture ; restauration.
- **005 — Exigences et tests :** transformer les scénarios UX-01 à UX-14 en critères observables et tests automatisés/manuels ; vérifier notamment la sémantique exacte du dialogue à trois boutons.

**Rappel :** aucune mission Codex n’est déclenchée par la publication de ce document. La conception et ses arbitrages restent prioritaires.
