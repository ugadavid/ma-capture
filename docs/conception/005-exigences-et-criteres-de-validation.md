# 005 — Exigences et critères de validation

| Propriété | Valeur |
|---|---|
| Projet | **Ma Capture** |
| Nature | Spécification des exigences de V0 et plan de recette |
| Version | **0.1** |
| Date | **14 novembre 2026** |
| Statut | **Cadrage fonctionnel consolidé — prêt pour relecture croisée des cinq documents, pas encore une mission Codex** |
| Sources | [001 — Vision et périmètre](001-vision-et-perimetre.md) · [002 — Parcours utilisateurs et ergonomie](002-parcours-utilisateurs-et-ergonomie.md) · [003 — Architecture et modèle de données](003-architecture-et-modele-de-donnees.md) · [004 — Intégrité, sauvegardes et cas limites](004-integrite-sauvegardes-et-cas-limites.md) |

> **Objet.** Transformer les décisions déjà prises en exigences **observables** et en scénarios de validation, sans élargir le périmètre. Ce document ne prescrit **ni implémentation immédiate, ni nombre arbitraire de tests, ni mission Codex**.
>
> **Lecture.** **[VALIDÉ]** désigne une décision du cadrage. **[À AFFINER]** désigne un détail d’implémentation ou d’ergonomie non décidé ; il ne faut pas le traiter comme une exigence ferme. En cas d’ancienne option encore mentionnée dans les documents 001 ou 002, les décisions arrêtées dans les documents 003, 004 et le présent document sont les plus récentes.

## 1. Périmètre et philosophie de la recette

**V0 =** capturer une zone, une fenêtre ou un écran ; annoter **immédiatement** sur la capture ; renseigner ou non une fiche documentaire ; enregistrer en bibliothèque ; retrouver et rééditer ; exporter ou copier l’image annotée ; récupérer simplement un travail interrompu.

**Hors périmètre :** import de fichiers image ; collage **entrant** d’images ; indexation ou scan des dossiers ; suivi des exports ; capture vidéo ; OCR ; retouche photo avancée ; calques multiples ; versionnage permanent ; capture en rafale ; multimoniteur avancé ; cloud ; serveur ; multiutilisateur.

L’application fonctionne **localement**, avec **une seule instance active**. Le bon fonctionnement se juge d’abord à la **fluidité d’usage et à la conservation correcte des données**, pas à la sophistication de l’infrastructure.

### 1.1 Les quatre derniers arbitrages [VALIDÉ]

| Réf. | Décision finale | Conséquence testable |
|---|---|---|
| V-001 | **Suppression volontaire avec confirmation** | Une capture ne disparaît pas si l’utilisateur annule la confirmation |
| V-002 | **Exporter ou copier l’état actuellement visible**, même s’il n’est pas enregistré | Un export ne déclenche pas l’archivage ni la modification de la fiche stockée |
| V-003 | **Miniatures avec annotations** | La bibliothèque montre le rendu annoté du dernier état enregistré |
| V-004 | **Zone rectangulaire par défaut au lancement** | Au démarrage, la commande de capture propose d’abord ce mode ; fenêtre et écran entier restent disponibles |

Le nettoyage technique **silencieux** des orphelins au lancement n’est **pas** une suppression volontaire : V-001 ne s’applique pas à ce nettoyage.

## 2. Convention des exigences

Les exigences sont numérotées **EX-xx**. Chacune est accompagnée d’un **critère d’acceptation**, qui doit pouvoir être observé dans l’interface, dans les bases/fichiers ou dans un test.

Types de vérification :

- **A — Automatisable :** données, logique, fichiers, transitions d’état et erreurs simulées.
- **M — Manuelle/visuelle :** gestes d’écran, apparence des annotations, lisibilité et ergonomie.
- **A+M :** combinaison des deux.

La lettre n’impose aucun framework de test ni seuil de couverture arbitraire. Une fonctionnalité est acceptée lorsqu’elle passe ses scénarios pertinents, pas lorsqu’un compteur de tests dépasse un nombre fixé au hasard.

## 3. Exigences — Capturer et enregistrer

| ID | Exigence [VALIDÉ] | Critère d’acceptation | Contrôle |
|---|---|---|---|
| EX-01 | Capturer **zone rectangulaire**, **fenêtre** et **écran entier** | Chacun des trois modes crée une image correspondant à la zone choisie | A+M |
| EX-02 | Mode initial : **zone rectangulaire** | Au lancement, le mode proposé est « Zone rectangulaire » ; les deux autres restent sélectionnables | M |
| EX-03 | Aucune création à l’abandon d’une sélection | Annuler la sélection avant prise de vue ne crée ni capture ni fiche vide | A+M |
| EX-04 | Afficher immédiatement la nouvelle image dans l’éditeur | Image à gauche, outils actifs, informations à droite, sans autre écran | M |
| EX-05 | Titre automatique et date de capture | Le titre est prérempli, la date/heure est générée sans saisie ; le titre est modifiable | A+M |
| EX-06 | Champs documentaires facultatifs | **Enregistrer** fonctionne même si le titre par défaut est la seule valeur textuelle présente | A+M |
| EX-07 | **Un seul** bouton Enregistrer et **Ctrl + S** | Bouton et raccourci produisent le même effet, sans dialogue documentaire préalable | A+M |
| EX-08 | Enregistrer capture, annotations et fiche ensemble sur le plan fonctionnel | Après fermeture puis réouverture, toutes les données du dernier enregistrement sont retrouvées | A |
| EX-09 | Nouvelle capture possible après succès d’Enregistrer | Aucun classement supplémentaire ni deuxième bouton n’est exigé avant de recommencer | M |
| EX-10 | Une seule capture non archivée active | Une nouvelle capture ne peut pas écraser un provisoire non enregistré sans appliquer le dialogue à trois choix | A+M |

**Précision EX-08 :** « ensemble » signifie un **résultat utilisateur cohérent**, pas l’obligation d’une transaction SQL interbases. Le mécanisme réel suit le document 003.

## 4. Exigences — Annotations et édition

| ID | Exigence [VALIDÉ] | Critère d’acceptation | Contrôle |
|---|---|---|---|
| EX-11 | Outils de base : sélection, ligne, flèche, rectangle, ellipse, texte | Chaque outil dessine ou sélectionne directement dans l’éditeur | M |
| EX-12 | Formes à contour uniquement | Un rectangle ou une ellipse laisse **visibles les pixels de l’image à l’intérieur**, quelle que soit la couleur choisie | M |
| EX-13 | Couleurs et épaisseurs modifiables | On peut définir un style avant dessin et modifier celui d’un objet existant | M |
| EX-14 | Objets rééditables | Sélectionner, déplacer, redimensionner, modifier un texte et supprimer un objet fonctionnent sans altérer l’image originale | A+M |
| EX-15 | Annuler/Rétablir pendant la séance | **Ctrl + Z** revient sur une modification, **Ctrl + Y** la rétablit ; aucune version permanente n’est créée | A+M |
| EX-16 | Zoom molette, 100 %, adapter à la fenêtre | Les annotations restent sur les mêmes régions de l’image originale à chaque zoom | A+M |
| EX-17 | Réouverture dans le même éditeur | Une capture déjà archivée retrouve ses annotations **modifiables**, pas une image aplatie | A+M |
| EX-18 | Séparer original et annotation | Un changement d’annotation modifie l’état courant des annotations, **pas** les octets du BLOB original | A |

**[À AFFINER]** Dimensions, palette par défaut, poignées de redimensionnement, limites de l’historique en mémoire, ordre de superposition et raccourcis non déjà décidés. Ces éléments sont des choix de détail à valider en recette graphique, pas un prétexte pour créer un logiciel de retouche complet.

## 5. Exigences — Documentation et bibliothèque

| ID | Exigence [VALIDÉ] | Critère d’acceptation | Contrôle |
|---|---|---|---|
| EX-19 | Fiche visible à droite pendant l’édition | Titre, date/heure, description, commentaire, catégorie et tags sont accessibles sans changer d’écran | M |
| EX-20 | **Zéro ou une catégorie** ; **plusieurs tags** | Une capture ne peut pas recevoir deux catégories simultanées, mais peut recevoir plusieurs tags | A+M |
| EX-21 | Modifier la documentation plus tard | Depuis la bibliothèque, on rouvre une capture, change ses champs et réenregistre en conservant son ID | A+M |
| EX-22 | Bibliothèque en **mosaïque et liste** | Les mêmes captures sont disponibles dans les deux vues | A+M |
| EX-23 | Recherche, tri et filtres simples | Recherche sur titre, description et commentaire ; filtres catégorie/tags ; tri date ou titre | A+M |
| EX-24 | Miniatures **avec annotations** | L’aperçu reflète le dernier rendu annoté **enregistré** ; une modification enregistrée actualise la miniature | A+M |
| EX-25 | Suppression volontaire confirmée | Une demande de suppression ouvre une confirmation ; **Annuler** laisse intactes la capture, l’image et les associations | A+M |
| EX-26 | Suppression volontaire complète après confirmation | Après validation, la capture, son image et ses liens `CaptureTags` ne subsistent plus | A |

**Précision EX-24 :** la miniature correspond au **dernier état enregistré** de la capture ; une annotation non encore enregistrée dans l’éditeur ne change pas la bibliothèque.

**[À AFFINER]** Gestion exacte de création/renommage des catégories et tags ; colonnes définitives de la liste ; filtre éventuel « À classer » ; dimensions et format des miniatures ; choix des icônes et mise en page. Les principes restent ceux des documents 001 à 003.

## 6. Exigences — Export et presse-papiers

| ID | Exigence [VALIDÉ] | Critère d’acceptation | Contrôle |
|---|---|---|---|
| EX-27 | Export en **PNG** et **JPEG** | Le fichier obtenu contient l’image avec les annotations visibles | A+M |
| EX-28 | Exporter **sans devoir enregistrer** d’abord | Sur une nouvelle capture ou une édition modifiée, l’export reflète l’**état visible actuel** | A+M |
| EX-29 | L’export ne vaut pas enregistrement | Après export seul, la nouvelle capture n’apparaît pas dans la bibliothèque ; une ancienne fiche modifiée n’est pas mise à jour en base | A |
| EX-30 | Copie **sortante** au presse-papiers | L’image annotée visible peut être collée dans une application tierce | M |
| EX-31 | Aucun suivi des exports | Aucun chemin, historique, surveillance ou statut de fichier exporté n’est enregistré par Ma Capture | A |
| EX-32 | Aucun import ni collage entrant | L’application ne propose pas de fonction d’ajout d’images existantes depuis le disque ou le presse-papiers | A+M |

La copie sortante suit le même principe que l’export : **rendre l’état visible** sans archiver ni modifier les données définitives.

## 7. Exigences — Données et stockage

| ID | Exigence [VALIDÉ] | Critère d’acceptation | Contrôle |
|---|---|---|---|
| EX-33 | Deux bases SQLite indépendantes | `catalogue.db` contient le métier ; `images.db` sert d’entrepôt binaire | A |
| EX-34 | Table `Images` minimale | Colonnes **`id`, `blob`, `sha256`** ; SHA stocké en **BLOB de 32 octets** | A |
| EX-35 | Identifiants entiers | `INTEGER PRIMARY KEY`, sans GUID/UUID ni générateur maison ; éventuelle réutilisation d’un ID supprimé acceptée | A |
| EX-36 | Une capture = une image | `Captures.image_id` pointe vers son BLOB ; aucun partage métier de BLOB entre captures en V0 | A |
| EX-37 | Annotations courantes dans la fiche | `Captures.annotations_json` contient l’état actuel ; aucune table d’historique permanent | A |
| EX-38 | Consultation sans chargement systématique des images complètes | Recherche, tri et liste/mosaïque exploitent le catalogue et les miniatures ; BLOB original chargé quand nécessaire | A |
| EX-39 | Pas de FK ni transaction interbase imposée | Le fonctionnement ne dépend pas de `ATTACH DATABASE` ou d’une coordination SQL distribuée | A |

Les tables catalogue prévues sont **Captures, Categories, Tags, CaptureTags**. `Captures` porte l’ID de l’image, les métadonnées, le JSON courant et la miniature. Les index et détails SQL internes pourront rester proportionnés aux besoins constatés.

## 8. Exigences — Provisoire, récupération, nettoyage

| ID | Exigence [VALIDÉ] | Critère d’acceptation | Contrôle |
|---|---|---|---|
| EX-40 | Nouvelle capture protégée par provisoire | Le **PNG** provisoire existe avant l’édition ; il reste récupérable avant Enregistrer | A |
| EX-41 | JSON provisoire actualisé pendant le travail | Après une courte temporisation, les dernières annotations et données textuelles **écrites avec succès** sont récupérables après arrêt brutal | A |
| EX-42 | Provisoire des anciennes captures modifiées | Le JSON référence le `capture_id` et les changements ; **pas de seconde copie du BLOB** | A |
| EX-43 | Ordre de sauvegarde initial | Écrire image + SHA dans `images.db`, puis fiche et relations dans `catalogue.db` | A |
| EX-44 | Supprimer le provisoire **après succès complet** | Aucun fichier de travail n’est supprimé tant que la fiche définitive n’a pas été validée, sauf abandon explicite | A |
| EX-45 | Récupérer une insertion BLOB interrompue | Après plantage entre les deux inserts, le provisoire est préservé ; l’image orpheline correspondante n’est pas supprimée | A |
| EX-46 | Reconnaître l’image provisoire déjà insérée | Priorité à l’ID provisoire éventuellement enregistré, puis comparaison du SHA-256 parmi les BLOB **orphelins** | A |
| EX-47 | Auto-check discret au démarrage | Comparaison des ID sans lire tous les BLOB ; orphelins non protégés supprimés **silencieusement** | A |
| EX-48 | Capture archivée sans image | Fiche devenue orpheline et liens documentaires supprimés silencieusement au démarrage | A |
| EX-49 | Suppression d’une capture archivée | Ordre : **image puis capture** ; une interruption peut être nettoyée au lancement | A |
| EX-50 | Reprise visible après plantage | L’image récupérable s’affiche dans l’éditeur avec les actions **Enregistrer / Ne pas enregistrer / Annuler** | A+M |
| EX-51 | Aucune suppression suite à « Annuler » | Le provisoire et le travail affiché restent disponibles ; une nouvelle capture demandée n’est pas lancée | A+M |
| EX-52 | Gestion de l’échec d’enregistrement | L’échec ne clôt pas l’édition, ne purge pas le provisoire et ne signale pas un succès trompeur | A+M |

**Précision EX-46 :** le SHA est une empreinte du **contenu binaire**, pas une preuve d’identité métier ni une clé unique. La recherche du SHA ne porte pas sur toute la bibliothèque, mais sur les images sans fiche pouvant correspondre au provisoire.

**[À AFFINER]** Temporisation exacte et format final du JSON provisoire, emplacement Windows, gestion d’un provisoire illisible. La règle d’usage est stable : **ne jamais écraser un travail récupérable sans décision explicite**.

## 9. Exigences — Dialogues, sauvegarde et restauration

| ID | Exigence [VALIDÉ] | Critère d’acceptation | Contrôle |
|---|---|---|---|
| EX-53 | Dialogue **Enregistrer / Ne pas enregistrer / Annuler** | Il apparaît quand quitter/naviguer/capturer à nouveau ferait perdre un travail non enregistré ; l’image reste visible | A+M |
| EX-54 | Sémantique des trois actions | Enregistrer = sauvegarder puis poursuivre si succès ; Ne pas enregistrer = abandonner puis poursuivre ; Annuler = rester et tout conserver | A+M |
| EX-55 | Sauvegarde manuelle par ZIP | L’archive créée contient **les deux bases** dans un état exploitable et cohérent | A |
| EX-56 | Sauvegarde avec travail non enregistré | Le dialogue à trois choix est proposé avant la sauvegarde ; le ZIP ne contient que les bases stables | A+M |
| EX-57 | Restauration manuelle d’un ZIP valide | Les deux bases sont restaurées ensemble ; le catalogue restauré est consultable | A+M |
| EX-58 | ZIP invalide ou incomplet | La restauration échoue clairement **sans remplacement des données courantes** | A |
| EX-59 | Restauration potentiellement destructrice | L’utilisateur est prévenu que restaurer remplace sa bibliothèque actuelle | M |
| EX-60 | Fonctionnement local et mono-utilisateur | L’application reste utilisable sans Internet, sans compte ni serveur | A+M |

Les sauvegardes/restaurations sont **manuelles**. Pas de sauvegardes planifiées, de réplication, de cloud ou de mécanismes de transaction interbases introduits pour ce seul besoin.

## 10. Reprise des scénarios UX-01 à UX-14

Les cas existants du [document 002](002-parcours-utilisateurs-et-ergonomie.md) sont conservés comme références de recette ; le tableau indique où les vérifier dans les exigences de ce document.

| Scénario source | Exigences associées |
|---|---|
| UX-01 — Capture avec titre automatique, sans saisie | EX-01, EX-04 à EX-09 |
| UX-02 — Encadré rouge et annotation texte | EX-11 à EX-14, EX-18 |
| UX-03 — Description et tags | EX-19 à EX-21 |
| UX-04 — Modification d’une capture existante | EX-17, EX-21, EX-37 |
| UX-05 — Annuler une nouvelle capture en présence d’un travail non sauvegardé | EX-10, EX-51, EX-53 à EX-54 |
| UX-06 — Ne pas enregistrer et poursuivre | EX-53 à EX-54 |
| UX-07 — Enregistrer avant fermeture | EX-07 à EX-08, EX-53 à EX-54 |
| UX-08 — Échec de sauvegarde avant fermeture | EX-52 à EX-54 |
| UX-09 — Plantage puis récupération | EX-40 à EX-46, EX-50 |
| UX-10 — Annuler la récupération sans perte | EX-50 à EX-51 |
| UX-11 — Recherche et bascule mosaïque/liste | EX-22 à EX-24 |
| UX-12 — Export PNG annoté | EX-27 à EX-29, EX-31 |
| UX-13 — Copie sortante vers le presse-papiers | EX-30, EX-32 |
| UX-14 — Zoom sans décalage des annotations | EX-16 |

**Ajouts explicitement décidés après la rédaction du 002 :** confirmation de suppression, export de l’état **non enregistré**, miniature **annotée**, capture **rectangulaire par défaut**. Ils sont testés via V-001 à V-004 et les exigences associées.

## 11. Reprise des scénarios INT-01 à INT-14

Les scénarios du [document 004](004-integrite-sauvegardes-et-cas-limites.md) restent la base des tests d’interruption et de reprise.

| Scénario source | Exigences associées |
|---|---|
| INT-01 — Plantage avant Enregistrer | EX-40, EX-50 |
| INT-02 — Travail courant et JSON provisoire | EX-41, EX-50 |
| INT-03 — BLOB inséré, fiche absente | EX-43 à EX-47 |
| INT-04 — Fiche insérée, provisoire encore présent | EX-43 à EX-47 |
| INT-05 — Image orpheline sans provisoire | EX-47 |
| INT-06 — Fiche orpheline sans image | EX-48 |
| INT-07 — Modifications d’une capture archivée | EX-42, EX-50 |
| INT-08 — Action Annuler | EX-51, EX-53 à EX-54 |
| INT-09 — Action Ne pas enregistrer | EX-53 à EX-54 |
| INT-10 — Échec d’écriture | EX-52 |
| INT-11 — Création ZIP | EX-55 à EX-56 |
| INT-12 — ZIP incomplet/invalide | EX-58 |
| INT-13 — Restaurer un ZIP valide | EX-57, EX-59 |
| INT-14 — Suppression interrompue après suppression de l’image | EX-48 à EX-49 |

## 12. Tests complémentaires indispensables à la V0

Les **28 scénarios de départ** ne couvrent pas, à eux seuls, les quatre dernières décisions ; on ajoute donc quelques recettes **ciblées**, sans en faire une deuxième usine à tests.

| Cas | Action | Résultat à constater |
|---|---|---|
| REC-01 | Lancer Ma Capture puis ouvrir le choix de capture | **Zone rectangulaire** présélectionnée |
| REC-02 | Dessiner un rectangle rouge autour d’un texte | L’intérieur conserve exactement la visibilité de l’image |
| REC-03 | Faire une annotation, **ne pas enregistrer**, puis exporter PNG/JPEG | L’export contient l’annotation visible, la bibliothèque ne change pas |
| REC-04 | Copier une annotation non enregistrée dans une autre application | Rendu actuel collé, sans modification de la fiche stockée |
| REC-05 | Enregistrer des annotations puis afficher la mosaïque | Miniature montrant ces annotations |
| REC-06 | Modifier et enregistrer une annotation ancienne | Miniature mise à jour ; image originale inchangée |
| REC-07 | Demander la suppression d’une capture puis **Annuler** | Capture, BLOB et tags présents |
| REC-08 | Refaire la suppression puis **Confirmer** | Capture, BLOB et liens supprimés |
| REC-09 | Démarrer avec une image orpheline correspondant au provisoire | Image protégée ; proposition de récupération |
| REC-10 | Démarrer avec orphelins sans provisoire | Nettoyage silencieux sans dialogue de diagnostic |

## 13. Critères de réception d’une V0

La V0 peut être considérée comme **fonctionnellement recevable** lorsque :

1. Tous les **EX-01 à EX-60** applicables sont satisfaits, sans écart critique connu.
2. Les **UX-01 à UX-14** du document 002 et les **INT-01 à INT-14** du document 004 sont vérifiés, avec résultat constaté.
3. Les **REC-01 à REC-10** ci-dessus sont vérifiés, en particulier les quatre décisions V-001 à V-004.
4. Les images originales restent intactes lors de la modification d’annotations ; les exports reflètent l’état visible, sans créer d’archivage implicite.
5. Le provisoire n’est jamais supprimé avant validation de `Captures` ou abandon explicite ; les tests d’interruption confirment la récupération prévue.
6. Une sauvegarde ZIP et une restauration réussies sont effectivement testées avec les **deux bases**.
7. Une **recette visuelle réelle sur Windows** confirme l’ergonomie et l’absence de boutons/dialogues inutiles, y compris dans l’éditeur.
8. L’application ne contient aucune fonctionnalité hors périmètre introduite sans validation préalable.

Aucun **nombre minimal arbitraire** de tests unitaires n’est fixé : les tests automatisés doivent couvrir les comportements déterministes et la recette humaine doit vérifier ce qu’un test de données ne peut pas démontrer.

### 13.1 Ce qui compte comme résultat vérifié

- **Automatisé :** assertion passant sur un état contrôlé (contenu DB, fichiers, empreinte SHA, statut, JSON, suppression/présence).
- **Manuel :** action effectuée dans l’application **réellement ouverte** avec observation de l’écran et de l’effet attendu.
- **Échec :** résultat différent de l’attendu, ou comportement non exercé alors qu’il est déclaré « validé ».
- **Non exécuté :** clairement indiqué comme tel, jamais comptabilisé comme réussite.

Les futures missions pourront fournir leurs comptes rendus de tests, captures d’écran et limites éventuelles ; **leur format détaillé sera choisi au lancement du développement**, pas imposé ici.

## 14. Détails à affiner sans bloquer la conception

Les documents précédents mentionnent des sujets qui ne sont **pas** des décisions fermes. Ils restent ouverts et ne doivent pas être comblés silencieusement par une mission :

- Identité visuelle finale, placement au pixel près, dimensions de fenêtre et de barre d’outils.
- Choix d’un éventuel raccourci **global** de capture (distinct de `Ctrl + S`) et des raccourcis d’export/copie.
- Dimensions et encodage exacts des miniatures ; comportement précis sur de très grandes bibliothèques.
- Détail des menus de gestion de catégories/tags et éventuel filtre « À classer ».
- Temporisation exacte de la sauvegarde automatique du JSON de récupération.
- Paramètres d’affichage des poignées d’annotation et taille de l’historique **en mémoire**.
- Choix final de la bibliothèque graphique, avec Python + PySide6 comme **orientation**, non comme contrainte technique démontrée.

Les comportements **déjà validés** ne sont pas réouverts à cause de ces détails. Si une difficulté technique réelle apparaît, elle doit être explicitement signalée avant de changer le périmètre.

## 15. Gouvernance et prochaine étape

Le dossier de conception comprend désormais :

1. [001 — Vision et périmètre](001-vision-et-perimetre.md)
2. [002 — Parcours utilisateurs et ergonomie](002-parcours-utilisateurs-et-ergonomie.md)
3. [003 — Architecture et modèle de données](003-architecture-et-modele-de-donnees.md)
4. [004 — Intégrité, sauvegardes et cas limites](004-integrite-sauvegardes-et-cas-limites.md)
5. **005 — Exigences et critères de validation** (présent document)

**Étape suivante : relire la cohérence des cinq documents** avant de rédiger des missions Codex. Les décisions les plus récentes prévalent, mais les formulations anciennes éventuellement contradictoires seront harmonisées lors de cette relecture.

**Rappel :** publier ce document **n’est pas** lancer une mission, créer du code, figer la maquette ou accepter une version de l’application.

**Principe final :** *une petite application bien définie, une recette utile et pas de centrale de la NASA.*
