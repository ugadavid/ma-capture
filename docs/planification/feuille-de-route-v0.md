# Ma Capture — Feuille de route prévisionnelle des missions Codex (V0)

| Propriété | Valeur |
|---|---|
| Projet | **Ma Capture** |
| Nature | **Planification** ; ni cahier des charges supplémentaire ni ordre de développement |
| Version | **0.1** |
| Date | **15 novembre 2026** |
| Statut | **Proposition structurée à valider**, aucune mission rédigée ou lancée |
| Horizon | Première version utilisable localement sous Windows (**V0**) |
| Références | [Conception 001–005](../conception/005-exigences-et-criteres-de-validation.md) · [guide graphique](../graphisme/guide-des-ecrans-ma-capture.md) · [prototype HTML](../graphisme/ma-capture-prototype.html) |

> **Important :** les identifiants **MC-001 à MC-014** décrivent **des lots prévisionnels**, pas des commandes à transmettre tels quels à Codex. Chaque mission sera rédigée et validée séparément, immédiatement avant son lancement. Les missions correctives ou exploratoires pourront s'intercaler **sans reprendre artificiellement toute la numérotation**.

## 1. Objectifs et méthode

Concevoir une petite application Windows **locale, mono-utilisateur, à instance unique**, qui capture, annote, documente, retrouve et exporte **uniquement ses propres captures**. Le dossier de conception et les maquettes ont été élaborés **avant** le développement.

La feuille de route vise à limiter les régressions et les réécritures :

- **Une mission = un résultat observable et vérifiable**, avec une taille raisonnable.
- **Ne jamais lancer simultanément toutes les missions** ni les considérer comme acceptées à l'avance.
- **Recette humaine réelle sur Windows**, surtout pour les interfaces et la prise de vue. Les tests automatisés complètent cette recette ; ils ne la remplacent pas.
- **Réutilisation des briques partagées** : identité visuelle, logo, composants graphiques, navigation, modales, accès aux deux bases, rendu des annotations.
- **Pas de nouveau périmètre implicite** sous prétexte qu'un élément apparaît dans le HTML de démonstration ou sur un PNG.
- **Aucune durée, date de livraison ou quantité arbitraire de tests fixée ici**. La séquence évoluera si la recette révèle un besoin de correction ou une dépendance réelle.

### Hiérarchie des références

1. **Documents [001](../conception/001-vision-et-perimetre.md), [002](../conception/002-parcours-utilisateurs-et-ergonomie.md), [003](../conception/003-architecture-et-modele-de-donnees.md), [004](../conception/004-integrite-sauvegardes-et-cas-limites.md), [005](../conception/005-exigences-et-criteres-de-validation.md)** : règles métier, architecture et critères d'acceptation.
2. **[ma-capture-prototype.html](../graphisme/ma-capture-prototype.html)** : apparence, répartition des espaces et interactions de référence, **pas** moteur Windows à transposer.
3. **[Guide graphique](../graphisme/guide-des-ecrans-ma-capture.md)** et ses **six PNG** (splash, bandeau, éditeur, popup, mosaïque, liste) : états visuels et limites de la simulation.

**Ne pas** confondre *composant graphique fidèle* et *fonction supplémentaire*. La palette des fonctions de V0 est délimitée par les spécifications.

## 2. Vue d'ensemble des 14 missions envisagées

| Mission | Objet | Condition principale d'entrée | Résultat attendu / recette majeure |
|---|---|---|---|
| **MC-001** | Socle Windows et identité graphique | Références 001–005 + HTML/PNG validées | Application native lançable ; splash, bandeau et structure des espaces reproduits |
| **MC-002** | Capture réelle | Socle UI disponible | Zone, fenêtre et plein écran capturés ; bandeau absent de l'image ; passage à l'éditeur |
| **MC-003** | Stockage initial et enregistrement | Capture fonctionnelle | Deux bases, provisoire, BLOB+SHA, fiche initiale ; réouverture après redémarrage |
| **MC-004** | Outils de dessin | Image affichée et stockage des annotations possible | Ligne, flèche, rectangle, ellipse, texte, couleurs/épaisseurs ; original préservé |
| **MC-005** | Édition des objets et zoom | Dessin de base fonctionnel | Sélection, déplacement, redimensionnement, texte éditable, Annuler/Rétablir et zoom |
| **MC-006** | Métadonnées documentaires | Catalogue initial opérationnel | Titre, date, description, commentaire, catégorie unique, tags ; modification ultérieure |
| **MC-007** | Modales et travail non enregistré | Éditeur et sauvegarde fonctionnels | Popups fidèles et dialogue Enregistrer / Ne pas enregistrer / Annuler |
| **MC-008** | Bibliothèque mosaïque et liste | Captures archivables et rendues miniatures disponibles | Deux vues du même catalogue, navigation et réouverture dans l'éditeur |
| **MC-009** | Recherche, filtres, catégories et favoris | Bibliothèque fonctionnelle | Recherche, À classer, tags, tri, étoiles/filtre Favoris persistants |
| **MC-010** | Exports et presse-papiers | Composition originale + annotations disponible | PNG/JPEG et copie sortante de l'état visible, même non enregistré |
| **MC-011** | Récupération et contrôle au lancement | Provisoires et bases en place | Reprise après incident, SHA, nettoyage silencieux des incohérences ordinaires |
| **MC-012** | Sauvegarde et restauration | Deux bases stabilisées | ZIP manuel cohérent, restauration complète et refus des archives invalides |
| **MC-013** | Raccourci global Windows | Bandeau et gestion d'instance fonctionnels | `Ctrl + Alt + C` ouvre le bandeau hors focus, sans voler `Win + Maj + S` |
| **MC-014** | Distribution et recette finale | Fonctionnalités précédentes intégrées | Exécutable/livraison Windows, vérification des exigences et recette complète |

### Étapes de consolidation

- **Lot A — Fondations :** MC-001 à MC-003. **Jalon :** capturer réellement et conserver une image sans intervention documentaire obligatoire.
- **Lot B — Éditeur :** MC-004 à MC-007. **Jalon :** annoter et documenter une capture, revenir la modifier sans perdre silencieusement le travail.
- **Lot C — Exploitation :** MC-008 à MC-010. **Jalon :** retrouver, filtrer, mettre en Favoris et exporter les captures.
- **Lot D — Robustesse et livraison :** MC-011 à MC-014. **Jalon :** reprise des interruptions, ZIP, raccourci global et version distribuable.

Ces jalons sont **des points de décision**, pas des versions commerciales obligatoires ni des dates.

## 3. Lot A — Fondations

### MC-001 — Socle Windows et identité graphique

**À construire :** structure de l'application native Windows, fenêtre/instance unique, configuration minimale, logo **commun**, splash **sans bouton** puis bandeau flottant **compact en haut au centre**, navigation vers les espaces Éditeur et Bibliothèque, composants de base réutilisables (boutons, champs, modales). Garder les interfaces au plus près du HTML/PNG validés. Le choix final du framework graphique est à examiner **dans cette mission**, avant de figer la base technique (Python + PySide6 n'est qu'une orientation du document 003).

**À ne pas prétendre :** si les bases et la réconciliation ne sont pas encore implémentées, le splash ne doit **pas afficher de fausses vérifications réussies**. Prévoir leur branchement ultérieur sans simulation trompeuse dans la version réelle.

**Recette :** application native démarrable sur Windows, logo identique sur toutes les vues, disposition générale conforme aux PNG, bandeau et navigation utilisables ; aucune véritable capture encore exigée.

**Références :** EX-61, guide G-01 à G-05 et G-03P, plus UX d'ouverture/navigation.

### MC-002 — Capture réelle de l'écran

**À construire :** rectangle (mode initial), fenêtre et plein écran ; sélection annulable ; le bandeau disparaît de la zone capturée ; l'image capturée arrive directement dans l'éditeur, avec le titre automatique. Pas d'import ou de collage entrant.

**Protection minimale dès cette mission :** dès qu'une **vraie capture utilisateur** existe, préserver ses octets dans un **PNG de travail persistant**, avant d'ouvrir les gestes d'édition ; ne pas annoncer une protection complète contre les plantages tant que MC-003/MC-011 ne la vérifient pas.

**Recette :** trois captures Windows réelles, aucune capture fantôme lors d'une annulation, image fidèle à la zone demandée et préservation provisoire minimale.

**Références :** EX-01 à EX-05, EX-40 (principe minimal), UX-01.

### MC-003 — Stockage initial et enregistrement

**À construire :** `catalogue.db` (Captures, Categories, Tags, CaptureTags) et `images.db` (Images : `id`, `blob`, `sha256` binaire 32 octets), entiers `INTEGER PRIMARY KEY`, statut Favori 0/1, une capture par image, `annotations_json` courant et miniature dans la fiche. PNG et JSON de travail à nom fixe ; enregistrement **image+SHA puis capture**, suppression du provisoire **seulement après réussite** de la fiche ou abandon explicite.

**Protection minimale dès cette mission :** si un INSERT échoue, **ne pas détruire** le provisoire. Le traitement complet des interruptions entre écritures sera renforcé en MC-011.

**Recette :** capture sauvée par un seul Enregistrer / `Ctrl + S` avec titre proposé, réouverture après relance, octets du BLOB retrouvés, SHA contrôlé, aucun fichier provisoire perdu prématurément.

**Références :** EX-06 à EX-09, EX-33 à EX-44.

## 4. Lot B — Éditeur complet

### MC-004 — Outils de dessin

**À construire :** Sélection, Ligne, Flèche, Rectangle, Ellipse, Texte ; contour coloré **sans remplissage opaque**, couleur/épaisseur, repère de l'image originale et stockage de l'état courant en JSON dans `Captures`.

**Recette :** chaque type dessiné dans l'éditeur réel ; formes transparentes, sauvegarde et réouverture sans altérer le BLOB original.

**Références :** EX-11 à EX-13, EX-18, UX-02.

### MC-005 — Manipulation, zoom et historique de séance

**À construire :** déplacement/redimensionnement/suppression d'annotation, édition de texte, Annuler/Rétablir **en mémoire seulement**, zoom molette/100 %/adapter ; coordonnées indépendantes du zoom.

**Recette :** modification puis sauvegarde d'un objet existant, repositionnement correct à tout zoom, original intact, aucun versionnage permanent.

**Références :** EX-14 à EX-18, UX-04 et UX-14.

### MC-006 — Fiche documentaire

**À construire :** panneau droit de la maquette ; titre automatique modifiable, date/heure non éditables, description, commentaire, **une catégorie au maximum** et plusieurs tags ; création simple des valeurs et modification après coup. Enregistrer **sans remplir** ces informations.

**Recette :** archive avec titre par défaut ; revenir et changer catégorie, tags, commentaire, titre, sans modifier l'ID de la capture.

**Références :** EX-05 à EX-08, EX-19 à EX-21, UX-03.

### MC-007 — Popups et navigation sécurisée

**À construire :** style des dialogues applicatifs conforme à **`03-editeur-popup.png`**. Navigation, fermeture et nouvelle capture avec changements non enregistrés : **Enregistrer / Ne pas enregistrer / Annuler**, dans cet ordre sémantique, sans abandon silencieux ; gestion des erreurs d'enregistrement. Confirmation de suppression volontaire.

**Recette :** les trois choix donnent trois issues différentes ; Annuler conserve tout, Enregistrer n'autorise la sortie qu'après succès ; aucun dialogue applicatif brutalement différent du prototype.

**Références :** EX-25, EX-50 à EX-54, EX-65, UX-05 à UX-10 et UX-17.

## 5. Lot C — Bibliothèque et exploitation

### MC-008 — Bibliothèque mosaïque et liste

**À construire :** deux présentations **du même catalogue**, navigation cohérente, cartes/ligne tabulaire, miniatures **annotées** du dernier état **enregistré**, ouverture dans le même éditeur ; interface fidèle aux PNG G-04/G-05. Pas de cases multi-sélection.

**Recette :** les mêmes fiches dans les deux vues ; miniature modifiée à chaque sauvegarde d'une annotation ; double-clic/ouverture fonctionnels, pas de chargement de tous les BLOB originaux pour une mosaïque.

**Références :** EX-17, EX-22, EX-24, EX-38, EX-66.

### MC-009 — Recherche, filtres et Favoris

**À construire :** recherche titre/description/commentaire, tri date/titre, filtres par catégorie et tags, **À classer = sans catégorie**, Favoris : étoile individuelle, statut persistant et filtre identique dans les deux vues. Pas de sélection multiple, lot ni arborescence de catégories.

**Recette :** recherches et filtres cohérents en mosaïque/liste ; marquer une étoile, basculer de vue et redémarrer sans perdre le statut.

**Références :** EX-20, EX-23, EX-63, EX-64, EX-66, UX-11 et UX-15.

### MC-010 — Export et presse-papiers

**À construire :** export PNG/JPEG d'un rendu original + annotations **actuellement visibles**, même non enregistrées ; copie sortante vers presse-papiers. Nom proposé issu du titre ; PNG par défaut ; dossier choisi par l'utilisateur. Après export, **aucun suivi** du fichier et aucun archivage implicite.

**Recette :** fichier/copie correcte avec une annotation non enregistrée ; la bibliothèque reste inchangée ; pas de table ou chemin d'export mémorisé.

**Références :** EX-27 à EX-32, UX-12, UX-13, REC-03, REC-04.

## 6. Lot D — Résilience et distribution

### MC-011 — Récupération et réconciliation

**À construire :** récupération d'une nouvelle capture (PNG+JSON de travail) ou d'une édition d'ancienne capture (JSON et `capture_id`) après crash ; détection de BLOB orphelins via ID puis SHA-256 du provisoire ; auto-check au démarrage, sans parcourir le disque ni hacher tout l'entrepôt ; suppression **silencieuse** des orphelins **non protégés**, image puis capture pour la suppression normale. Brancher le splash réel sur ces étapes.

**Important :** ceci **complète** les garanties minimales déjà présentes en MC-002/003, il ne faut pas créer une période où une vraie capture est jetée avant enregistrement. Une difficulté dans ce domaine autorise **d'avancer cette mission** dans le calendrier.

**Recette :** interruption simulée avant/après chaque étape d'enregistrement ; provisoire préservé ; récupération visible dans l'éditeur et suppression silencieuse des résidus ordinaires uniquement.

**Références :** EX-40 à EX-52, INT-01 à INT-10 et INT-14, REC-09/10.

### MC-012 — Sauvegarde et restauration manuelles

**À construire :** deux bases sauvegardées ensemble dans un ZIP via une méthode SQLite adéquate, restauration de l'ensemble, validation d'archive, confirmation du remplacement et dialogue en cas de travail non enregistré. **Pas** de sauvegarde planifiée ni cloud.

**Recette :** ZIP exploitable, restauration complète, ZIP invalide refusé sans remplacement de la bibliothèque courante.

**Références :** EX-55 à EX-59, INT-11 à INT-13.

### MC-013 — Raccourci global Windows

**À construire :** `Ctrl + Alt + C` enregistré globalement si disponible ; invoque le petit bandeau depuis une autre application, ne capture pas immédiatement, ne vole pas `Win + Maj + S`, ne crée pas une seconde instance. Si la réservation échoue, avertissement discret et lancement manuel toujours possible.

**Recette :** test dans un autre logiciel Windows, test du conflit avec un raccourci déjà réservé, maintien de l'outil Windows.

**Références :** EX-62, UX-16, REC-12. La personnalisation du raccourci est **V2**, hors de cette mission.

### MC-014 — Distribution et recette finale

**À construire :** livrable Windows autonome (portable et/ou installateur selon décision ultérieure), identité graphique complète, vérification des chemins de données et sauvegardes, contrôle de l'instance unique, documentation de lancement et d'utilisation.

**Recette :** lancement sur Zenith Ice en conditions réelles, parcours complet, tests spécifiques de capture native, popups conformes et **recette finale des EX-01 à EX-66**, des scénarios UX/INT/REC applicables ; rapport des résultats réellement exécutés et des limites.

**Important :** un rapport de tests automatiques ne remplace **jamais** l'inspection visuelle Windows ; aucun résultat ne peut être annoncé « testé » sans exécution effective.

## 7. Dépendances et souplesse du plan

Le chemin de référence est :

`MC-001 → MC-002 → MC-003 → MC-004 → MC-005 → MC-006 → MC-007 → MC-008 → MC-009 → MC-010 → MC-011 → MC-012 → MC-013 → MC-014`.

Il ne constitue **pas** une contrainte rigide :

- **MC-002 et MC-003 :** si la capture native exige un ajustement du provisoire, avancer la petite brique de stockage nécessaire ; aucun travail réel ne doit être perdu faute de mission d'intégrité encore exécutée.
- **MC-004/005/006 :** partager un éditeur commun plutôt que refaire les composants ; reprendre la structure de données de MC-003 sans créer un second modèle.
- **MC-007 :** le style de popup est à prévoir dès MC-001, mais le dialogue complet peut attendre la maîtrise des états non enregistrés ; des protections minimales sont nécessaires avant toute navigation destructrice.
- **MC-008/009 :** une recherche de base peut émerger pendant la bibliothèque ; conserver le découpage si cela évite de concentrer trop de code dans une seule livraison.
- **MC-011 :** avancer si les premiers tests révèlent un risque de perte ou si les mécanismes provisoires doivent être sécurisés avant les annotations avancées.
- **MC-012/013 :** peuvent être replanifiées si la recette révèle une dépendance, sans étendre leur périmètre.
- Une mission peut être **subdivisée** (ex. `MC-004a`, `MC-004b`) si elle devient trop lourde. Les corrections suivent une **mission corrective identifiée** (ex. `MC-005-C1`), discutée avant exécution. Ce sont **des exemples de nommage**, pas des missions existantes.

**Aucune refonte de l'architecture validée ne doit être introduite en douce.**

## 8. Contrat de préparation et de réception d'une mission

Avant toute mission, rédiger un fichier de mission distinct avec :

1. **Identifiant, objectif unique, périmètre explicite** et exclusions.
2. **État d'entrée** : commits, fonctionnalités déjà réalisées, fichiers existants à réutiliser (pas de reconstruction gratuite).
3. **Références à consulter** dans les 001–005, guide, HTML et PNG utiles ; les priorités entre ces sources.
4. **Travaux demandés**, risques connus et choix laissés à Codex. Toute nouvelle décision produit remonte pour validation.
5. **Critères de réussite** : comportement visible, tests automatisés pertinents, recette manuelle Windows.
6. **Livrables** : code, éventuelles migrations, rapport factuel, captures si interface, commits et état Git propres.
7. **Limites et suite** : ce qui n'a pas pu être testé, corrections requises, points devant être arbitrés par l'utilisateur.

Après retour de Codex : **relecture du rapport → recette réelle → acceptation ou mission corrective → seulement ensuite mission suivante**. Codex ne s'auto-attribue pas l'acceptation finale.

## 9. Ce que cette feuille de route n'autorise pas

- Elle **ne lance aucune mission** et **ne valide pas MC-001**.
- Elle ne fixe **aucune date de livraison**, ne promet aucune durée et ne préjuge d'aucun résultat technique.
- Elle ne transforme pas les **six PNG** ou les fausses captures du HTML en fichiers à importer ou en fonctionnalités non demandées.
- Elle n'autorise **ni GUID, ni ATTACH interbases, ni serveur, ni OCR, ni masques/flou, ni versionnage permanent, ni sélection multiple, ni fonctions cloud**.
- Elle ne remplace pas les [exigences de recette](../conception/005-exigences-et-criteres-de-validation.md) ni les exigences de protection du provisoire définies dans le [document 004](../conception/004-integrite-sauvegardes-et-cas-limites.md).

## 10. Prochaine action

**Rédiger soigneusement MC-001**, dans un document distinct, en détaillant le socle natif, les composants graphiques à partager, le rôle du splash tant que le stockage n'existe pas et la recette réelle sur Windows. **Faire valider ce document avant d'ouvrir la cage à Codex.**
