# MC-001 — Socle Windows natif et identité graphique de Ma Capture

| Élément | Valeur |
|---|---|
| Projet | Ma Capture — `ugadavid/ma-capture` |
| Mission | **MC-001** |
| Nature | Première mission de **réalisation**, lot A (fondations) |
| Statut | **PROJET DE MISSION — À RELIRE ET VALIDER PAR DAVID ; NE PAS EXÉCUTER SUR LA SEULE PRÉSENCE DE CE FICHIER** |
| Préparation | 15 novembre 2026 |
| Version applicative initiale visée | **`alpha-0.1.0`** (première application lançable) |
| Branche commune | `main` / `origin/main` |
| Prérequis | Dépôt documentaire existant, aucune application développée à ce jour |

> **Autorisation :** ce document est une proposition publiée pour relecture. Il **ne donne pas à Codex l'ordre d'agir**. Le développement commence uniquement lorsque David transmet explicitement la mission validée à Codex. Ne pas passer à MC-002 sans nouvelle décision.

## 1. Intention et résultat de cette mission

Faire naître **une vraie application de bureau Windows**, reconnaissable comme **Ma Capture**, avec une base de code simple et exploitable par les missions suivantes.

À la fin de MC-001, on doit pouvoir **lancer l'application sous Windows 11**, observer un démarrage court et honnête, accéder au **bandeau flottant compact en haut de l'écran** et parcourir les **coquilles graphiques de l'éditeur et de la bibliothèque** avec une identité visuelle fidèle à notre prototype HTML et à ses PNG.

**Il ne s'agit ni de reproduire le moteur JavaScript du prototype, ni de livrer toutes les fonctionnalités visibles dans les maquettes.** MC-001 prépare l'interface et la navigation, sans capture réelle, sans édition de pixels, sans catalogue fonctionnel.

**Objectif principal unique :** créer le **socle d'application et le système visuel natif réutilisable**. Si la réalisation fidèle des quatre états visuels devient trop lourde, le signaler et proposer une subdivision de cette mission plutôt qu'élargir le chantier en silence.

## 2. État d'entrée à vérifier

Le dépôt possède déjà :

- `AGENTS.md` — règles permanentes, **prioritaires pour cette mission comme pour sa rédaction** ;
- `README.md` ;
- `docs/conception/001-vision-et-perimetre.md` à `005-exigences-et-criteres-de-validation.md` ;
- `docs/planification/feuille-de-route-v0.md` ;
- `docs/graphisme/ma-capture-prototype.html` ;
- `docs/graphisme/guide-des-ecrans-ma-capture.md` et six captures PNG.

La racine du dépôt **ne contient initialement aucun code applicatif**. Il s'agit donc de créer le projet, **sans réécrire** les documents validés.

**Avant intervention :** vérifier le dépôt, la branche et `git status` ; mettre à jour sa connaissance de `origin/main` sans écraser de travaux locaux ni forcer Git. Inspecter réellement les références graphiques ; ne pas se contenter des titres de fichiers.

## 3. Références et ordre de lecture

**Obligatoires avant de commencer :**

1. **[`AGENTS.md`](../AGENTS.md) intégralement**, surtout « alpha jetable — ZÉRO migration », versionnement lent et interdiction d'élargissement.
2. [Guide graphique](../docs/graphisme/guide-des-ecrans-ma-capture.md) et [prototype HTML](../docs/graphisme/ma-capture-prototype.html) : **cible d'interface**, non technologie imposée.
3. Les PNG [01-splash](../docs/graphisme/01-splash.png), [02-bandeau](../docs/graphisme/02-bandeau.png), [03-editeur](../docs/graphisme/03-editeur.png), [03-editeur-popup](../docs/graphisme/03-editeur-popup.png), [04-bibliotheque-mosaique](../docs/graphisme/04-bibliotheque-mosaique.png) et [05-bibliotheque-liste](../docs/graphisme/05-bibliotheque-liste.png).
4. [001 — Vision et périmètre](../docs/conception/001-vision-et-perimetre.md), surtout section 6 ; [002 — Ergonomie](../docs/conception/002-parcours-utilisateurs-et-ergonomie.md), surtout section 2 ; [005 — Exigences](../docs/conception/005-exigences-et-criteres-de-validation.md), en particulier EX-61 et EX-65.
5. Consulter [003 — Architecture](../docs/conception/003-architecture-et-modele-de-donnees.md) et [004 — Intégrité](../docs/conception/004-integrite-sauvegardes-et-cas-limites.md) **uniquement pour identifier les frontières des missions futures**, sans les implémenter maintenant.

**Priorités :** 001–005 définissent **ce qui est autorisé** ; HTML, guide et PNG définissent **à quoi cela doit ressembler**. Les images de démonstration, faux chiffres, onglets externes du simulateur et commandes simulées ne sont pas des fonctionnalités à coder.

## 4. Travail attendu

### 4.1 — Choisir et installer le socle graphique natif

- Examiner brièvement la solution la plus simple pour reproduire correctement les maquettes dans une **application de bureau Windows 11** ; **Python + PySide6 est une orientation plausible, pas un choix imposé**.
- Retenir **un seul** framework graphique adapté, avec justification **courte et factuelle** dans le rapport : rendu, gestion des fenêtres sans cadre, positionnement du bandeau, styles et maintenance.
- Mettre en place uniquement les dépendances réellement nécessaires, une arborescence légère, un point d'entrée clair et une **commande simple de lancement depuis PowerShell**.
- Aucun serveur local, aucune interface web embarquée pour afficher le fichier HTML, aucun système de plugins, aucun framework maison de composants, aucune couche de persistance inventée en avance.
- Prévoir une structure de code suffisamment claire pour que les futures missions puissent ajouter les comportements derrière les vues **sans refaire les écrans**.

### 4.2 — Identité graphique et briques réutilisées

- Reprendre **le même logo SVG stylisé de Ma Capture** visible dans le splash, le bandeau, l'éditeur et la bibliothèque ; respecter la signature **Trucabule** sur le splash.
- Partager intelligemment les styles et éléments récurrents : palette claire bleutée/violette, boutons, icônes simples, espaces, titres, champs, états sélectionnés ; préparer le **style commun des popups applicatifs** de G-03P.
- Préférer des contrôles réellement natifs au framework choisi, **visuellement proches de la maquette** ; ne pas faire de chaque écran une implémentation graphique indépendante.
- Les polices et ressources doivent être utilisables légalement et se limiter à ce qui est nécessaire ; ne pas publier des ressources propriétaires ou des images personnelles tirées des captures de démonstration.
- Ajuster raisonnablement les proportions selon la taille réelle de l'écran et la mise à l'échelle Windows ; ne pas imposer une fenêtre figée à une résolution unique.

### 4.3 — Splash de démarrage (G-01)

- Petite fenêtre centrée, anthracite, accents bleus, logo, « Ma Capture » et « Trucabule », **sans bouton de validation**.
- Le splash disparaît automatiquement une fois la **réelle initialisation du socle graphique** terminée et laisse place au bandeau.
- **AUCUNE fausse vérification SQLite.** Dans cette mission, `catalogue.db`, `images.db`, leur réconciliation et la récupération du provisoire **n'existent pas encore**. Ne pas afficher « catalogue ouvert », « images vérifiées », « références réconciliées » ou une progression arbitraire comme si cela avait été effectué.
- On peut montrer sobrement « Initialisation de l'interface » **seulement si cette opération est réellement en cours** ; pas de pourcentage factice ni d'attente volontaire pour permettre de voir l'animation.
- Garder une organisation simple permettant **plus tard** de brancher les contrôles de MC-003/MC-011 ; ne pas créer maintenant leur infrastructure.

### 4.4 — Bandeau flottant de capture (G-02)

- Fenêtre **compacte, horizontale et indépendante**, placée **en haut et au centre de l'écran**, avec ombre et angles arrondis similaires à G-02.
- Logo commun et commandes **Plein écran**, **Rectangle** (état sélectionné initial), **Fenêtre**, **Bibliothèque**, et **croix de fermeture seule** ; pas de gros écran d'accueil à la place.
- **Bibliothèque** ouvre l'espace Bibliothèque vide ; **Fermer** quitte effectivement l'application.
- Les trois modes de capture peuvent, dans MC-001, **changer leur état visuel de sélection** ; en revanche **aucune véritable capture n'est implémentée**. Aucun bouton ne doit laisser croire qu'une capture a été réalisée : si la commande n'est pas opérationnelle, son indisponibilité doit être compréhensible (par exemple aide discrète/état désactivé). Ne pas afficher une image inventée comme résultat.
- Pas de raccourci global `Ctrl + Alt + C` encore : c'est **MC-013**. Ne pas intercepter `Win + Maj + S`.
- Prévoir le retour simple depuis l'espace Bibliothèque vers le bandeau, sans seconde instance.

### 4.5 — Coquille de l'Éditeur (G-03)

Préparer la **structure visuelle** de l'éditeur, à utiliser ultérieurement par MC-002/004/005/006 :

- En-tête Ma Capture / Édition / Bibliothèque, identité et commandes conformes à G-03.
- Zone à gauche avec **outils d'annotation libellés** ; vaste espace d'image **central** ; panneau documentaire **à droite**, avec l'organisation des champs et son style.
- Barre de commandes inférieure, avec **un seul Enregistrer**, export et indicateur d'état à leur emplacement de référence.
- Sans capture, l'état de la zone centrale doit être un **état vide assumé** (par exemple « Aucune capture en cours »). Pas de fausse capture utilisateur ni de données de démonstration importées.
- Les outils, champs, Enregistrer/Exporter/zoom et raccourcis non implémentés doivent être **visuellement désactivés ou identifiés comme non disponibles** : **pas de simulation trompeuse** d'un travail réellement conservé.
- La maquette définit l'apparence : **aucun outil de dessin, gestion d'annotations, validation documentaire ou BLOB** dans cette mission.

Pour la recette graphique, un moyen **léger et exclusivement de développement** de prévisualiser l'apparence de l'éditeur est autorisé ; ce mécanisme ne doit pas devenir une fonctionnalité utilisateur ni nécessiter un gestionnaire de données fictives.

### 4.6 — Coquille de Bibliothèque (G-04 et G-05)

- Préparer **le même espace** Bibliothèque avec navigation latérale, en-tête, recherche/filtre/tri visibles et **bascule Mosaïque / Liste** opérationnelle au niveau de la **présentation**.
- État normal à ce stade : **catalogue vide**, aucune capture persistée et aucune miniature d'exemple ajoutée dans les données.
- L'interface doit rester proche des deux PNG, y compris les zones, libellés, couleurs et proportions ; les commandes de recherche, catégories, favoris et suppression ne sont **pas encore fonctionnelles** et doivent être clairement inactives en l'absence de catalogue.
- Prévoir le retour vers le bandeau et la navigation de l'application sans créer de fausses fiches. **Pas de tables métier ni de tests de recherche** à ce stade.
- Les catégories sont plates ; aucune sélection multiple, aucune commande de traitement en masse.

### 4.7 — Popups : style commun, pas encore logique complète

- Préparer le **style réutilisable** des dialogues applicatifs selon **G-03P**, éventuellement à travers un exemple de prévisualisation **non intégré au parcours utilisateur**.
- Ne pas installer partout des boîtes de dialogue Windows brutes en remplacement du style validé.
- **Ne pas implémenter maintenant** le moteur de changements non enregistrés, les confirmations de suppression ni le parcours de récupération : ce sera MC-007 et MC-011.
- Un choix standard de fichier Windows pourra rester natif lorsque la future fonction d'export/backup le demandera.

### 4.8 — Version initiale et lancement

- Si cette mission fournit le **premier programme lançable**, identifier sa version applicative comme **`alpha-0.1.0`**, issue d'une **source unique** dans le code/configuration.
- Ne pas incrémenter la version à chaque commit de documentation. **Ne jamais** passer en `0.2.0` sans décision explicite de David.
- Un **lancement développeur simple** suffit pour MC-001 (par exemple un script PowerShell ou une commande Python documentée) : **pas d'installateur, de packaging portable définitif, de signature de code ou de Microsoft Store** avant la mission correspondante.

## 5. Hors périmètre — frontière stricte

**Ne pas réaliser pendant MC-001 :**

- aucune capture d'écran réelle (MC-002), interception de raccourci global (MC-013), capture multi-écran avancée ;
- aucune base de données, table, ORM, données de test persistées ou migration (MC-003), **AUCUN mécanisme de migration SQLite** ;
- aucun PNG/JSON provisoire de récupération, contrôle de SHA ou réconciliation (MC-003/011) ;
- aucun dessin, texte annotatif, zoom métier, Annuler/Rétablir réel (MC-004/005) ;
- aucune sauvegarde documentaire, catégories, tags, Favoris fonctionnels (MC-006/009) ;
- aucun dialogue de perte de travail fonctionnel ou suppression métier (MC-007) ;
- aucune vraie bibliothèque alimentée, recherche ou miniature annotée (MC-008/009) ;
- aucun export PNG/JPEG, presse-papiers sortant ni ZIP de sauvegarde/restauration (MC-010/012) ;
- aucun installeur final ni travail de distribution (MC-014).

**Règle permanente d'alpha :** les futures bases et données applicatives de **test** sont **jetables**. Quand un futur schéma change, les bases de test seront supprimées/recréées, **sans compatibilité ascendante, sans conversion et sans migration**. Cette règle engage **aussi la rédaction des futures missions par ChatGPT** ; seul David pourra y mettre fin explicitement, même après MC-225.

## 6. Choix laissés à Codex et points à remonter

**Autonomie technique locale permise :** noms internes des classes/modules, framework de rendu natif (après rapide justification), mise en forme du thème, commandes de démarrage, découpage minimal des composants et tests utiles.

**À remonter avant modification :** toute divergence substantielle entre la maquette et une contrainte technique Windows, tout besoin de changer les parcours, d'ajouter des dépendances lourdes ou de modifier le périmètre. Ne pas présenter un changement visible de design comme « détail technique ».

**Sans attente de décision :** si une icône demande un léger ajustement technique mais conserve la même intention et la même lisibilité, Codex peut poursuivre et la documenter. Ne pas interrompre David pour un pixel.

## 7. Recette / critères d'acceptation de MC-001

La mission ne peut être qualifiée de **complète** que si les observations suivantes sont vérifiées **réellement** :

| Réf. | Vérification | Preuve minimale |
|---|---|---|
| MC1-01 | Démarrage de l'application sur **Windows 11** par la commande documentée | Commande exécutée et résultat indiqué |
| MC1-02 | Splash anthracite, logo/signature et disparition automatique **sans faux contrôles** ni pause artificielle | Inspection réelle, capture si possible |
| MC1-03 | Bandeau **compact en haut au centre**, mode Rectangle sélectionné, croix de fermeture seule | Capture Windows réelle + comparaison à G-02 |
| MC1-04 | Bibliothèque accessible depuis le bandeau, navigation retour, fermeture sans processus résiduel évident | Parcours manuel |
| MC1-05 | Les vues Éditeur, Bibliothèque mosaïque et liste présentent des **coquilles fidèles** avec état vide honnête et commandes non implémentées non trompeuses | Captures réelles + comparaison aux PNG |
| MC1-06 | Logo, couleurs et composants communs cohérents, popup **préparable dans le style G-03P** | Inspection du rendu et des ressources |
| MC1-07 | Bascule de présentation **Mosaïque/Liste** effective, sans faux catalogue | Test UI manuel |
| MC1-08 | Pas de capture réelle, pas de données SQLite, pas de migration, pas de service/serveur ou dépendance lourde non justifiée | Inspection code + diff |
| MC1-09 | Version initiale **`alpha-0.1.0`** conservée dans une source unique | Vérification des métadonnées du code |
| MC1-10 | Lancement/test sous Windows documentés honnêtement, tests et état Git vérifiés | Rapport factuel et SHA Git si publication autorisée |

**Recette de David :** l'acceptation graphique définitive appartient à David après essai sur son environnement. Une capture générée ou un test automatisé **ne vaut pas** validation humaine.

**Si un test graphique ne peut pas être effectué** dans l'environnement de Codex, rendre le travail en verdict **PARTIEL** avec précisément les écrans restant à tester. Ne jamais fabriquer une preuve.

## 8. Livrables à produire lors de l'exécution autorisée

- Code natif de **Ma Capture** et ressources graphiques nécessaires, structurés avec sobriété.
- **Commande exacte** pour lancer l'application sur la VM et, si applicable, sur Zenith Ice (prérequis explicites).
- Fichier de version unique portant `alpha-0.1.0`.
- **Rapport** `reports/MC-001-socle-windows-et-identite-graphique.md` : framework retenu et pourquoi, fonctionnalités réellement créées, fichiers modifiés, captures, résultats des tests, défauts constatés, limites.
- **Captures d'écran réelles** des états accessibles (splash si capturable, bandeau, éditeur vide, bibliothèque mosaïque et liste, éventuellement référence de style des popups), dans un sous-dossier clairement identifié de `reports/`. Ne pas reprendre les captures du prototype en les faisant passer pour des preuves de l'application.
- Diff relu, absence de secrets/données personnelles et dépôt propre ou écarts expliqués.

Lors du **lancement explicite par David**, Codex pourra publier les seuls changements autorisés de la mission sur `origin/main`, après contrôle de la divergence Git, et vérifier le SHA distant. **Jamais de `push --force`** ; un résultat partiel/NO-GO utile doit être restitué honnêtement.

## 9. Règle d'arrêt et suite

**S'arrêter après MC-001**, rapport publié et recette proposée. Ne pas enchaîner MC-002, ne pas inventer de base, ne pas ajouter des fonctions pour que la démonstration « fasse davantage réel ».

David et ChatGPT examineront les preuves et les interfaces ; ils décideront si MC-001 est validée, si une correction **MC-001-C1** est nécessaire ou si le travail doit être subdivisé.

**Rappel non négociable :** ce fichier est actuellement **une mission préparée, pas une mission lancée**.
