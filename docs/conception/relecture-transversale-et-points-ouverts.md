# Relecture transversale — Cohérence et points encore ouverts

| Propriété | Valeur |
|---|---|
| Projet | **Ma Capture** |
| Date | **14 novembre 2026** |
| Périmètre | Documents [001](001-vision-et-perimetre.md), [002](002-parcours-utilisateurs-et-ergonomie.md), [003](003-architecture-et-modele-de-donnees.md), [004](004-integrite-sauvegardes-et-cas-limites.md) et [005](005-exigences-et-criteres-de-validation.md) |
| Nature | **Relecture documentaire et registre des choix**, pas une mission Codex |
| Statut | Relecture effectuée ; décisions majeures harmonisées ; détails ouverts identifiés |

## 1. Résultat de la relecture

**Aucune contradiction de périmètre bloquante ne subsiste entre les principes de V0 après harmonisation.**

Les premières rédactions de 001 et 002 conservaient des hypothèses exploratoires alors que les documents suivants avaient déjà arbitré leur sort. Elles ont été **remplacées par les décisions réellement prises**, sans ajouter de fonctionnalités :

| Document harmonisé | Corrections documentaires |
|---|---|
| **001 — Vision** | Modes zone/fenêtre/écran et zone par défaut ; dialogue à trois choix ; copie sortante et export de l'état visible ; Annuler/Rétablir sans versions ; **1 capture = 1 image** ; `INTEGER PRIMARY KEY` ; **deux bases** ; `Images(id, blob, sha256)` ; PNG/JSON provisoires ; retrait de l'ancien tableau d'arbitrages désormais clos |
| **002 — Ergonomie** | Zone rectangulaire par défaut ; versionnage permanent exclu ; récupération de l'image et du JSON effectivement écrit ; miniatures avec annotations ; suppression volontaire avec confirmation ; export/copie de l'état visible non enregistré ; retrait des anciens choix techniques à arbitrer |
| **003 — Architecture** | Miniatures du dernier rendu annoté enregistré ; suppression volontaire confirmée ; export/copie indépendants de la sauvegarde de la fiche |
| **004 — Intégrité** | Distinction explicite entre **suppression volontaire confirmée** et **nettoyage automatique silencieux** |
| **005 — Exigences** | Les quatre dernières décisions (V-001 à V-004) y figurent déjà, ainsi que 60 exigences EX-01 à EX-60, 14 scénarios UX, 14 scénarios INT et 10 cas REC supplémentaires |

**Limite de cette relecture :** il s'agit de la **cohérence des textes**, pas d'une preuve technique, d'une maquette validée ni d'une recette exécutée. Aucun test d'application n'a encore été mené.

## 2. Invariants désormais fermes — ne plus les rouvrir par accident

- **Application locale, Windows, mono-utilisateur, instance unique.**
- **Trois modes de capture** ; zone rectangulaire présélectionnée au démarrage.
- **Seulement les captures réalisées par Ma Capture** : ni import d'image, ni collage entrant, ni scan de fichiers.
- **Deux vues** : éditeur avec image annotable à gauche et panneau documentaire facultatif à droite ; bibliothèque avec mosaïque/liste.
- **Une seule action Enregistrer** (Ctrl + S), titre automatique, une catégorie maximum, plusieurs tags.
- Annotations immédiatement modifiables : **une couche logique**, l'original intact, **aucun versionnage permanent**.
- **Deux SQLite indépendants** : `catalogue.db` pour le métier, `images.db` pour `Images(id, blob, sha256)`, SHA-256 binaire de **32 octets**.
- **1 capture ↔ 1 image**, identifiants entiers générés par SQLite, aucune FK interbase.
- **PNG + JSON provisoires**, pas de capture en rafale ; le provisoire est conservé jusqu'au succès de la fiche ou à l'abandon explicite.
- Auto-check rapide au démarrage ; **orphelins ordinaires supprimés silencieusement**, sauf image liée à un provisoire à récupérer.
- **Suppression volontaire avec confirmation** ; ordre de suppression technique : image puis capture.
- **Exporter / Copier l'état visible même non enregistré**, sans sauvegarder la capture et sans suivre le fichier exporté.
- Miniatures représentant le **dernier état enregistré avec annotations**.
- Sauvegarde/restauration **manuelles** des **deux bases ensemble** dans un ZIP.
- Dialogue de perte potentielle : **Enregistrer / Ne pas enregistrer / Annuler**, avec trois effets distincts.

## 3. Questions à trancher ensemble, si nous le souhaitons, avant les missions

Ces éléments sont réellement **ouverts**. Les propositions ci-dessous sont des **recommandations**, **pas des décisions validées**.

| Réf. | Question | Option simple proposée | Incidence |
|---|---|---|---|
| **O-01** | **Accueil au lancement** : affiche-t-on la bibliothèque ou l'éditeur vide avec « Capturer » ? | Bibliothèque avec un bouton Capturer visible | Ergonomie de départ |
| **O-02** | **Créer et gérer catégories/tags** : le panneau de droite suffit-il à créer les valeurs manquantes, ou faut-il un mini écran dédié pour renommer/supprimer ? | Création depuis la fiche ; gestion simple dans la bibliothèque, sans module autonome | Parcours documentaire |
| **O-03** | **Filtre « À classer »** : doit-il exister en V0 ? Que signifie non classée ? | Filtre facultatif pour les captures sans catégorie (sans forcer le renseignement) | Bibliothèque |
| **O-04** | **Raccourci global de capture** : indispensable dès V0 ou reporté ? | Reporté : bouton Capturer d'abord, `Ctrl + S` déjà validé | Intégration Windows |
| **O-05** | **Nom proposé à l'export** et choix de format par défaut | Reprendre le titre de la capture, proposer PNG et laisser le dialogue Windows choisir le dossier | Export quotidien |

Aucune de ces décisions n'exige de revoir la structure des deux bases ni les principes du document 004.

## 4. Détails qui peuvent attendre les maquettes ou la première recette

Ils **ne doivent pas bloquer** le lancement du projet s'ils restent ouverts jusqu'à une maquette testable.

| Réf. | Détail ouvert | Moment raisonnable |
|---|---|---|
| **O-06** | Thème, couleurs de l'interface, taille des panneaux, disposition exacte des commandes, comportement des petites fenêtres | Maquettes et recette graphique |
| **O-07** | Palette et épaisseurs proposées par défaut, style des étiquettes texte, poignées et superposition des objets | Recette de l'éditeur |
| **O-08** | Colonnes de la vue Liste, tri initial, règles de recherche multi-termes, combinaisons de filtres | Recette de la bibliothèque |
| **O-09** | Taille, encodage et stratégie d'actualisation technique des miniatures annotées | Implémentation et tests |
| **O-10** | Gestion exacte du focus clavier, de `Suppr` et de `Ctrl + Z` dans les champs texte | Recette ergonomique |
| **O-11** | Comportement pour créer, renommer ou fusionner des tags/catégories homonymes | Selon la décision O-02 |

**À ne pas confondre avec une question ouverte :** le **contenu** des miniatures (**avec annotations**) et la **confirmation des suppressions** sont déjà validés.

## 5. Détails purement techniques — à laisser à Codex, sous contrôle de la recette

| Réf. | Point | Contrainte déjà décidée |
|---|---|---|
| **T-01** | Choix final de la bibliothèque GUI (Python + PySide6 envisagés) | Application Windows locale, édition directe agréable, ergonomie conforme |
| **T-02** | Dossier local réel des bases et du provisoire | Aucun cloud ni dossier scanné ; provisoire persistant entre lancements |
| **T-03** | Durée exacte de temporisation et écriture du JSON de récupération | Conserver le dernier JSON correctement écrit, sans bloquer les gestes |
| **T-04** | Mode de journalisation SQLite et copies de sauvegarde ZIP | Pas de transactions interbases ; les deux bases sauvegardées comme un ensemble cohérent |
| **T-05** | Cas de plusieurs BLOB orphelins ayant le même SHA et traitement d'un JSON provisoire incomplet | Ne pas perdre le provisoire ; correspondance par ID prioritaire |
| **T-06** | Séquence exacte de remplacement/restauration des deux fichiers | Ne pas prétendre qu'un ZIP incomplet ou invalide a restauré la bibliothèque |
| **T-07** | Empaquetage Windows (exécutable portable et/ou installateur), icône de l'application | À choisir lors de la préparation d'une première distribution, pas maintenant |

**Ces points n'autorisent pas l'ajout d'un serveur, de GUID, d'un `ATTACH DATABASE`, de transactions distribuées, d'un moteur de versions ou d'une télémétrie.**

## 6. Vigilances de relecture, sans nouvelle architecture

- Le comportement du dialogue à trois choix **lors d'une sauvegarde/restauration manuelle avec modifications non enregistrées** est décrit dans le document 004. Il faudra vérifier qu'il reste compréhensible en usage réel : sauvegarder le ZIP ne doit pas être confondu avec **Enregistrer la capture**.
- Le nettoyage silencieux du catalogue au démarrage est un **choix explicite du projet**. Il ne s'agit pas d'une fonction de réparation générale. La priorité donnée au provisoire est impérative.
- Le SHA-256 aide à identifier les octets d'une image interrompue ; il **n'est pas** un ID métier ni un dispositif de déduplication globale.
- Le contenu exporté peut provenir d'annotations **non enregistrées** ; la miniature de bibliothèque ne doit montrer que les **annotations enregistrées**.
- Les exigences du document 005 sont une **cible de validation**, non la preuve que les tests sont déjà passés.

## 7. Organisation de la suite

1. **Discuter éventuellement O-01 à O-05** : ce sont les seuls choix ergonomiques restants qui méritent une décision explicite avant la première mission.
2. Laisser **O-06 à O-11** aux maquettes et à la recette ; laisser **T-01 à T-07** à l'implémentation encadrée par les critères du document 005.
3. Une fois les choix importants arrêtés, préparer **des missions Codex de taille limitée**, chacune avec ses critères de recette. **Aucune mission n'est lancée par cette relecture.**

**Conclusion :** la conception fonctionnelle est suffisamment cohérente pour préparer le travail ; **elle ne nécessite pas une nouvelle étude d'architecture**.
