# 004 — Intégrité, sauvegardes et cas limites

| Propriété | Valeur |
|---|---|
| Projet | **Ma Capture** |
| Nature | Document de conception — protection des données et comportement après interruption |
| Version | **0.3 — références de dialogues explicitées** |
| Date | **14 novembre 2026** |
| Statut | **Cadrage fonctionnel et technique** ; critères de recette à formaliser dans le document 005 |
| Documents précédents | [001 — Vision et périmètre](001-vision-et-perimetre.md) · [002 — Ergonomie](002-parcours-utilisateurs-et-ergonomie.md) · [003 — Architecture et modèle de données](003-architecture-et-modele-de-donnees.md) |
| Document suivant | 005 — Exigences et critères de validation |

> **Principe :** protéger les captures et les modifications en cours avec quelques règles simples et vérifiables. Il s'agit d'une **application Windows locale, mono-utilisateur, à instance unique**, pas d'une application distribuée. **Aucune mission Codex n'est déclenchée par ce document.**

## 1. Les trois décisions complémentaires validées

**S-001 — Sauvegarde régulière du JSON provisoire.** Après un changement d'annotation ou de métadonnées documentaires, le JSON de travail est actualisé automatiquement avec une **courte temporisation**, sans écrire sur disque à chaque déplacement de souris.

**S-002 — Récupération des modifications sur une capture déjà archivée.** Si une ancienne capture est rouverte puis modifiée, ses changements non enregistrés peuvent être repris après un plantage. Il suffit de conserver dans le JSON provisoire l'**ID de la capture** et les modifications de la fiche/annotations ; **le BLOB original n'est pas recopié**.

**S-003 — Sauvegarde et restauration manuelles.** L'application propose **Sauvegarder** et **Restaurer**, avec une **archive ZIP contenant les deux bases**. Pas de sauvegarde planifiée, de cloud ni de réplication.

Ces décisions complètent les [principes de stockage du document 003](003-architecture-et-modele-de-donnees.md).

## 2. Périmètre exact de la protection

Deux situations sont distinctes :

| Situation | Contenu de référence | Provisoire |
|---|---|---|
| **Nouvelle capture non archivée** | L'image originale dans `capture-en-cours.png` | JSON avec titre, annotations, textes et tags en cours |
| **Capture archivée, puis modifiée** | BLOB original dans `images.db`, fiche enregistrée dans `catalogue.db` | JSON avec `capture_id` et **état de travail modifié** |

**[VALIDÉ]** Une seule capture est éditée à la fois. Les fichiers provisoires ont donc des noms stables, dans un **répertoire local persistant propre à Ma Capture**, et ne servent pas de système de capture en rafale. Les noms de travail retenus dans le document 003 sont **`travail/capture-en-cours.png`** pour une nouvelle image et **`travail/capture-en-cours.json`** pour l'état éditable (y compris lors de la modification d'une capture archivée). Le PNG n'est pas nécessaire lorsque le BLOB original existe déjà.

**[VALIDÉ]** La protection provisoire est une sécurité **avant** la sauvegarde normale ; elle **n'est pas** un historique de versions. Le seul état définitif des annotations est `Captures.annotations_json` ; Annuler/Rétablir n'existe que pour la séance d'édition.

## 3. Écriture du provisoire

### 3.1 Nouvelle capture

1. La capture est réalisée par l'application.
2. L'image originale est écrite en **PNG provisoire**, avant toute édition.
3. Un JSON provisoire contient le titre par défaut, les champs et les annotations courantes ; il peut aussi porter l'ID du BLOB si celui-ci a déjà été inséré lors d'une sauvegarde interrompue.
4. Le même écran montre l'image à gauche et la fiche à droite ; aucun champ documentaire n'est obligatoire avant **Enregistrer**.

### 3.2 Modifications après enregistrement

Lorsqu'une fiche existante est ouverte, il n'est **pas nécessaire** de créer une seconde copie PNG. Le JSON provisoire associe les valeurs modifiées à son **`capture_id`**. En cas de plantage, l'image sera relue depuis l'entrepôt binaire.

### 3.3 Actualisation automatique du JSON [VALIDÉ]

- Toute modification des annotations ou des champs documentaires marque la séance comme modifiée.
- Le JSON de récupération est réécrit après une **courte temporisation**, pour éviter les écritures incessantes lors d'un dessin ou d'une saisie.
- À la fin d'une action ou lors d'une fermeture normale, la dernière version du JSON doit être prise en compte avant tout abandon de l'écran.
- Une écriture simple et prudente convient : écrire un **fichier compagnon de remplacement**, puis substituer le JSON précédent une fois l'écriture réussie.
- Si le JSON ne peut pas être mis à jour, **ne pas écraser ni supprimer le précédent** ; la saisie reste visible dans l'éditeur et un message clair peut prévenir que la protection provisoire est indisponible.

**[À PARAMÉTRER EN IMPLÉMENTATION]** La durée exacte de temporisation. Le document 005 vérifiera surtout le résultat observable, pas une valeur choisie arbitrairement ici.

## 4. Enregistrement définitif d'une nouvelle capture

La séquence arrêtée est volontairement linéaire :

```text
Capture PNG provisoire + JSON provisoire
                    │
          L'utilisateur choisit Enregistrer
                    │
         1. Insérer BLOB + SHA-256 (32 octets) dans images.db
                    │
         2. Insérer Captures et ses relations dans catalogue.db
                    │
         3. Valider l'enregistrement de la fiche
                    │
         4. Seulement alors, supprimer les fichiers provisoires
```

**Règle impérative :** si l'étape 2 ou 3 échoue, **le provisoire est conservé**, même si le BLOB a déjà été inséré.

Le SHA-256 est calculé sur les **octets exacts du PNG provisoire**. Il accompagne le BLOB dans la table `Images(id, blob, sha256)` ; il ne sert ni d'identifiant principal ni de mécanisme global de déduplication.

Si l'application s'arrête après l'insertion BLOB, mais avant celle de la fiche :

- le PNG et le JSON provisoires sont encore disponibles ;
- l'auto-check au prochain lancement **ne supprime pas** le BLOB orphelin qui correspond à ce provisoire ;
- la récupération peut **réutiliser le BLOB déjà créé**, en privilégiant l'ID éventuellement mémorisé dans le JSON, puis le SHA en cas d'absence de cet ID ;
- la validation de `Captures` permet enfin de purger le provisoire.

**Cas particulier simple :** si l'enregistrement de la fiche a réussi mais que l'application s'arrête **avant** la suppression du provisoire, l'application vérifie au démarrage que l'image est déjà liée à la fiche, puis nettoie le reliquat **sans créer de doublon**.

Aucune transaction **interbases** ni `ATTACH DATABASE` ne fait partie de la conception.

## 5. Enregistrer les modifications d'une capture archivée

L'image originale ne change pas. L'utilisateur modifie le texte, la catégorie, les tags et/ou les annotations ; **Enregistrer / Ctrl + S** actualise `catalogue.db` seulement.

- Tant que les changements ne sont pas enregistrés, le **JSON provisoire** est maintenu à jour selon la section 3.
- Après un **enregistrement réussi**, le JSON provisoire de cette séance peut être supprimé.
- Si l'écriture échoue, **ne pas supprimer** le JSON et ne pas quitter l'éditeur comme si l'opération avait réussi.
- Lors d'un redémarrage après interruption, l'application retrouve la capture par son **ID**, recharge l'image originale depuis `images.db` et propose le travail documentaire/graphique récupéré depuis le JSON.

L'application ne conserve pas les versions précédentes : elle préserve seulement **la dernière version enregistrée** et, tant qu'il y a une séance non terminée, **le travail provisoire récupérable**.

## 6. Dialogue unique en cas de risque de perte [VALIDÉ]

L'image doit rester **visible derrière le dialogue** :

```text
                 Modifications non enregistrées

     [ Enregistrer ]   [ Ne pas enregistrer ]   [ Annuler ]
```

| Bouton | Comportement |
|---|---|
| **Enregistrer** | Enregistrer la capture ou les modifications ; poursuivre l'action demandée **uniquement après succès** |
| **Ne pas enregistrer** | Abandonner explicitement les modifications en cours et le provisoire correspondant, puis poursuivre |
| **Annuler** | **Annuler l'action demandée**, rester sur le travail en cours et **ne rien supprimer** |

Ce même principe s'applique à une nouvelle capture demandée alors que l'actuelle est non enregistrée, à la fermeture, au changement de fiche ou à la navigation qui abandonnerait l'édition.

**Référence visuelle :** les popups applicatifs suivent [03-editeur-popup.png](../graphisme/03-editeur-popup.png) et le [guide d'écran](../graphisme/guide-des-ecrans-ma-capture.md). Leur style ne modifie pas la sémantique des trois boutons ni les règles de protection du provisoire ; les fenêtres standards de choix de fichiers Windows peuvent rester natives.

En cas de récupération au démarrage :
- **Enregistrer** archive ou actualise la capture récupérée ;
- **Ne pas enregistrer** abandonne explicitement le travail récupéré ;
- **Annuler** laisse l'image récupérée ouverte, **sans purge**, pour décider plus tard.

Une fermeture **normale** sans modification en attente ne demande aucune confirmation. Si un provisoire de travail a été conservé alors que l'enregistrement a bien réussi, il peut être nettoyé après vérification.

## 7. Auto-check au démarrage : court, automatique et silencieux

Au lancement, l'application contrôle les références **sans charger les BLOB originaux**.

**Ordre nécessaire :**

1. Examiner l'existence d'un **provisoire récupérable**.
2. Si un PNG provisoire existe, calculer son SHA-256 ; prendre également l'ID éventuellement indiqué dans le JSON.
3. Comparer les `Images.id` aux `Captures.image_id`, sans scanner le disque ni ouvrir tous les contenus binaires.
4. **Protéger** une image orpheline qui correspond à la capture en récupération.
5. Supprimer silencieusement les autres orphelins, selon le tableau suivant.
6. Présenter la récupération de la capture en cours s'il y en a une, **image visible dans l'éditeur**.

| État détecté | Action |
|---|---|
| Image + fiche présentes | Ne rien faire |
| Image sans fiche correspondant au provisoire | **Conserver** ; proposer la récupération |
| Image sans fiche, sans correspondance avec un provisoire | **Supprimer l'image silencieusement** |
| Fiche sans image | **Supprimer la fiche et ses liens silencieusement** |
| Provisoire d'une capture déjà enregistrée | Vérifier qu'elle est bien enregistrée, puis nettoyer le provisoire |
| JSON de modifications d'une fiche archivée encore valide | **Conserver** pour proposer la reprise de l'édition |

Le SHA permet une correspondance du contenu ; il n'est **pas** supposé unique entre toutes les captures du monde. On cherche en priorité l'**ID provisoire associé**, lorsqu'il existe, et on ne recherche des SHA correspondants que parmi les **images orphelines**.

**Aucune boîte de dialogue ni rapport d'audit pour les orphelins ordinaires**. Les dialogues concernent uniquement le travail effectivement récupérable ou une erreur empêchant l'application de fonctionner.

Il ne s'agit **pas** d'un contrôle général de corruption SQLite ni d'une vérification intégrale du SHA de tous les BLOB au lancement.

## 8. Suppression volontaire d'une capture

Une capture archivée et son image constituent une paire au même cycle de vie, sans partage de BLOB entre captures.

Ordre retenu :

1. **Supprimer l'image** de `images.db`.
2. **Supprimer la fiche** de `catalogue.db`, avec ses associations `CaptureTags`.

Si une interruption survient entre les deux, la **fiche sans image** est détectée puis supprimée silencieusement au prochain démarrage.

Les catégories et tags qui peuvent être utilisés par d'autres fiches ne sont pas supprimés automatiquement.

**[VALIDÉ]** La suppression **volontaire** d'une capture dans la bibliothèque demande **confirmation**, présentée dans le style de la popup de référence. Si l'utilisateur annule, la capture, son BLOB et ses associations sont conservés. Le nettoyage technique des orphelins au démarrage, lui, reste **silencieux**, sans confirmation.

## 9. Sauvegarde manuelle : deux bases, un ZIP [VALIDÉ]

L'application propose **Sauvegarder** depuis son interface.

Fonctionnement retenu :

- L'utilisateur choisit un emplacement pour l'archive.
- L'application prépare une copie cohérente de **`catalogue.db` et `images.db`**, puis les place **ensemble dans un ZIP**.
- Pour ne pas capturer un état intermédiaire, l'application **suspend ses propres écritures** pendant la préparation des copies ; elle ne tente pas d'archiver un fichier SQLite en cours de modification sans précaution.
- Le ZIP est déclaré réussi **uniquement lorsque les deux bases ont été copiées correctement** et que l'archive est terminée.
- En cas d'échec, un message simple est affiché ; la sauvegarde n'est pas présentée comme valide.

**[ORIENTATION TECHNIQUE]** Utiliser le mécanisme de sauvegarde de SQLite pour produire les copies, plutôt que copier à l'aveugle des fichiers de base ouverts. Aucun service, ordonnanceur, processus permanent ni mécanisme distribué.

**Provisoire en cours :** avant une sauvegarde manuelle, si un travail est non enregistré, afficher le dialogue habituel **Enregistrer / Ne pas enregistrer / Annuler**. Ainsi, l'archive contient **uniquement les deux bases dans un état stable** ; aucun format supplémentaire de « sauvegarde de brouillons » n'est introduit.

**[VALIDÉ]** Aucune sauvegarde automatique planifiée, réplication ou synchronisation cloud.

## 10. Restauration manuelle [VALIDÉ]

La commande **Restaurer** permet de sélectionner un ZIP créé par Ma Capture.

- Si un travail est non enregistré, appliquer d'abord le dialogue à trois choix.
- Vérifier que l'archive contient **les deux bases attendues** et qu'elles sont exploitables.
- Fermer ou suspendre les accès aux bases avant de les remplacer.
- Restaurer les **deux bases ensemble**, pas une seule choisie isolément.
- Relancer le contrôle ordinaire de cohérence et rafraîchir la bibliothèque après restauration.
- Si l'archive est invalide ou incomplète, **ne pas remplacer les données courantes** ; afficher une erreur compréhensible.

Le remplacement se fait avec l'application hors écriture. Les détails matériels de remplacement des fichiers et du redémarrage éventuel sont à choisir à l'implémentation, sans multiplier les écrans.

**Attention :** restaurer un ancien ZIP ramène le catalogue et les images **à la date de ce ZIP** ; ce n'est pas un versionnage automatique. Le geste de restauration doit donc être clairement présenté comme un **remplacement de la bibliothèque actuelle**.

## 11. Erreurs et limites : rester proportionné

- **Échec d'enregistrement d'une nouvelle capture :** garder le PNG et le JSON provisoires, rester dans l'éditeur, afficher une erreur.
- **Échec de mise à jour d'une fiche :** conserver ses changements en mémoire et son provisoire ; ne pas prétendre que l'enregistrement a réussi.
- **Interruption pendant un enregistrement :** reprendre avec le provisoire au lancement, avec le SHA et l'ID si nécessaire.
- **Provisoire PNG incomplet ou illisible :** ne pas inventer d'image ; afficher une erreur claire plutôt que déclarer une récupération réussie.
- **Base impossible à ouvrir :** distinguer cette erreur des simples orphelins ; ne pas exécuter un nettoyage aveugle si le catalogue ou l'entrepôt sont inaccessibles.
- **Erreur de sauvegarde/restauration ZIP :** signaler l'échec sans afficher de faux succès.
- **Absence de connexion Internet :** fonctionnement inchangé.

Aucune télémétrie, journal d'audit métier, surveillance des exports, réparation automatique complexe ni infrastructure de secours ne sont requis.

## 12. Scénarios à transformer en tests (document 005)

| Réf. | Situation | Résultat attendu |
|---|---|---|
| INT-01 | Capture réalisée puis arrêt brutal avant Enregistrer | PNG provisoire conservé et récupération proposée |
| INT-02 | Annotations et champs modifiés, puis arrêt brutal | Dernier JSON provisoire validement écrit retrouvé |
| INT-03 | BLOB inséré puis arrêt brutal avant création de `Captures` | Provisoire conservé ; BLOB correspondant non purgé |
| INT-04 | `Captures` insérée, arrêt brutal avant suppression du provisoire | Pas de doublon ; reliquat provisoire nettoyé après vérification |
| INT-05 | Image orpheline sans provisoire correspondant | Image supprimée silencieusement au démarrage |
| INT-06 | Fiche sans image | Fiche et liens supprimés silencieusement |
| INT-07 | Capture archivée modifiée, plantage avant Enregistrer | Reprise proposée par `capture_id` et JSON, sans copie du BLOB |
| INT-08 | « Annuler » dans le dialogue de perte potentielle | Action initiale annulée, aucun travail supprimé |
| INT-09 | « Ne pas enregistrer » dans ce dialogue | Abandon explicite, nettoyage du provisoire |
| INT-10 | Échec de l'écriture dans une base | Provisoire conservé et erreur visible |
| INT-11 | Sauvegarde manuelle réussie | ZIP contenant les deux bases, exploitable pour restauration |
| INT-12 | ZIP invalide ou incomplet lors de la restauration | Données courantes non remplacées |
| INT-13 | Restauration d'un ZIP valide | Catalogue et images de l'archive rétablis ensemble |
| INT-14 | Suppression interrompue après effacement de l'image | Fiche résiduelle supprimée au prochain démarrage |

Ces scénarios ne sont **pas** encore des tests exécutés : ils servent à rédiger les exigences et la recette du document 005.

## 13. Bilan des décisions

| Réf. | Décision |
|---|---|
| S-001 | JSON provisoire actualisé automatiquement après les modifications avec une courte temporisation |
| S-002 | Édition d'une capture archivée récupérable via `capture_id` et JSON, sans recopier son BLOB |
| S-003 | Sauvegarde et restauration **manuelles**, par ZIP contenant les deux bases |
| S-004 | Un seul travail en cours ; fichier(s) provisoire(s) local(aux), à nom stable |
| S-005 | Le provisoire d'une nouvelle capture est supprimé **uniquement après succès** de la fiche définitive ou abandon explicite |
| S-006 | Ordre création : image + SHA, puis fiche ; ordre suppression : image, puis fiche |
| S-007 | Auto-check au démarrage basé sur les **ID**, SHA utilisé pour le provisoire, purge **silencieuse** des orphelins non protégés |
| S-008 | Dialogue **Enregistrer / Ne pas enregistrer / Annuler** pour les pertes potentielles ; récupération montrant l'image |
| S-009 | Pas d'historique permanent ; pas de mécanisme de capture en rafale |
| S-010 | Aucune transaction interbase ou architecture multi-utilisateur ajoutée |
| S-011 | Les deux bases doivent être sauvegardées et restaurées comme un **ensemble cohérent** |
| S-012 | Aucune mission de développement déclenchée |

**Conclusion :** une capture non encore archivée reste sur disque, une fiche archivée retrouve son image par ID, le SHA permet d'identifier une insertion interrompue, le démarrage nettoie les orphelins et une commande ZIP assure les sauvegardes manuelles. **Le reste doit rester simple.**
