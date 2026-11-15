# Ma Capture — Guide des écrans pour Codex

**Statut : projet de consignes graphiques à valider, avant toute mission de développement.**  
**Emplacement prévu :** `docs/graphisme/guide-des-ecrans-ma-capture.md`  
**Références fonctionnelles :** `docs/conception/001` à `005`, ainsi que le registre des arbitrages.  
**Public :** équipe de réalisation / Codex, une fois les consignes approuvées.

## 0. Comment utiliser ces maquettes

Les quatre fichiers PNG ci-dessous sont les **références visuelles approuvées**. Ils représentent cinq **états/écrans** : splash screen, bandeau de capture, éditeur, bibliothèque mosaïque, bibliothèque liste.

| Référence | Fichier PNG présent dans ce dossier | Rôle |
|---|---|---|
| G-01 | [Écran de chargement Ma Capture.png](<Écran de chargement Ma Capture.png>) | Écran de démarrage et vérification |
| G-02 | [Ma Capture sur fond bleu abstrait.png](<Ma Capture sur fond bleu abstrait.png>) | Petit bandeau flottant de capture |
| G-03 | [Ma Capture _ annotation de Glencoe.png](<Ma Capture _ annotation de Glencoe.png>) | Éditeur / documentation |
| G-04 | [Bibliothèque Ma Capture en deux modes.png](<Bibliothèque Ma Capture en deux modes.png>) | Vue mosaïque **en haut** et vue liste **en bas** : deux états de la **même** interface |

**Priorité des sources :** les PNG indiquent l'apparence et la disposition visuelle ; les documents de conception définissent **ce que l'application fait réellement**. Un bouton, un exemple d'image, un chiffre ou un champ dessiné par la génération graphique **n'autorise pas à créer une fonctionnalité absente du périmètre**. Les textes, dates, fichiers et nombres de captures visibles dans les maquettes sont illustratifs.

### Ligne graphique commune

- Application Windows contemporaine, sobre et lisible ; grandes zones aérées, angles légèrement arrondis, icônes simples, contrastes suffisants.
- Splash screen volontairement **anthracite avec accents bleus**, conformément à la maquette G-01.
- Bandeau, éditeur et bibliothèque dans le **thème clair bleuté** visible sur G-02 à G-04, avec touches **violet doux / bleu / turquoise**. Respecter les **variations effectivement validées** entre les écrans au lieu d'en inventer une uniformisation.
- Le fond bleu de Windows et les photographies servent à **mettre en situation** les écrans, pas à devenir un fond imposé de l'application ni des ressources à importer en bibliothèque.
- Les dimensions de la maquette sont une **intention de proportions**, pas une obligation de calquer des pixels sur une résolution donnée.

## 1. G-01 — Splash screen / chargement

![Référence G-01](<Écran de chargement Ma Capture.png>)

### Apparence

- Petite fenêtre de démarrage centrée, panneau anthracite sombre, coins arrondis, ombre douce.
- Logo de capture en bleu à gauche ; nom **Ma Capture** et signature **Trucabule**.
- Indicateurs simples pour les étapes : ouverture du catalogue, vérification des images, réconciliation des références, préparation de l'interface.
- Barre de progression et état lisible, sans surcharge de messages techniques.

### Comportement

1. Apparaît au lancement de l'application, avant le bandeau de capture.
2. Ouvre `catalogue.db` et `images.db` ; vérifie les références d'images **sans charger tous les BLOB** ; préserve les données provisoires récupérables et supprime silencieusement les orphelins ordinaires selon les documents 003/004.
3. Prépare l'interface puis disparaît automatiquement. Si les vérifications sont immédiates, **ne pas ralentir artificiellement le lancement** pour montrer une animation ou un pourcentage.
4. Si une session interrompue est récupérable, la montrer ensuite **dans l'éditeur, image visible**, avec **Enregistrer / Ne pas enregistrer / Annuler**.
5. Une erreur empêchant le démarrage doit être compréhensible ; la suppression d'orphelins ordinaires ne déclenche ni alerte ni rapport visible.

**Attention :** le « ~70 % » de la maquette est une illustration, **pas une valeur fixe**. Pas de moteur de réparation élaboré ni de parcours de configuration.

## 2. G-02 — Bandeau flottant de capture

![Référence G-02](<Ma Capture sur fond bleu abstrait.png>)

### Apparence

- **Petit bandeau horizontal**, posé **en haut et au centre de l'écran**, pas une grande fenêtre d'accueil.
- Coins arrondis, fond clair légèrement translucide, ombre discrète, identité Ma Capture à gauche.
- Cinq commandes lisibles : **Plein écran**, **Rectangle** (sélectionné par défaut), **Fenêtre**, **Bibliothèque**, **Fermer**.
- La commande sélectionnée se distingue par un bleu doux, comme sur la maquette.

### Comportement

- **Plein écran** : capture l'écran choisi par le fonctionnement normal de V0 ; aucune gestion multimoniteur avancée demandée.
- **Rectangle** : commence la sélection rectangulaire à la souris ; mode initial après lancement.
- **Fenêtre** : permet de désigner une fenêtre à capturer.
- **Bibliothèque** : ouvre la bibliothèque documentaire, sans créer de capture.
- **Fermer** : ferme l'application, après protection éventuelle du travail non enregistré.
- Pendant la sélection/capture, le bandeau **ne doit pas figurer dans l'image capturée**. Si la capture est annulée, aucune fiche vide n'est créée.
- Après une capture réussie, afficher **directement l'éditeur** (G-03).

### Raccourci clavier global

- Un **raccourci global distinct du `Win + Maj + S` de Windows** est souhaité pour Ma Capture, afin de laisser le choix entre les deux outils.
- **Combinaison exacte à arrêter** après vérification des conflits ; ne pas choisir silencieusement le raccourci Windows.
- **V2 seulement :** permettre à l'utilisateur de personnaliser ce raccourci dans les paramètres.
- Ceci est distinct de **Ctrl + S** dans l'éditeur, qui sauvegarde la capture.

## 3. G-03 — Éditeur / annotation et documentation

![Référence G-03](<Ma Capture _ annotation de Glencoe.png>)

### Apparence et disposition

- Fenêtre principale organisée en **trois zones** : barre d'outils d'annotation à gauche ; **grande image éditable** au centre ; **fiche documentaire** à droite.
- En haut : identification Ma Capture, accès Édition/Bibliothèque, titre et quelques outils de navigation/zoom.
- En bas : état de sauvegarde, commande d'export et **un seul bouton Enregistrer (Ctrl + S)**. Le panneau documentaire est redimensionnable.
- Garder le style validé de la maquette : fond clair, contours fins, accent violet pour les actions sélectionnées, champs lisibles.

### Édition de l'image

- La capture est **directement modifiable dès son apparition**, sans autre fenêtre ni mode spécial.
- Outils **dans la V0** : **Sélection**, **Ligne/Trait**, **Flèche**, **Rectangle**, **Ellipse**, **Texte**, couleur et épaisseur ; **Annuler/Rétablir** sur la séance.
- Formes de mise en évidence : **contours colorés, intérieur entièrement transparent**. Les annotations restent sélectionnables, déplaçables, redimensionnables et recolorables.
- **Zoom à la molette, 100 %, adapter à la fenêtre** ; les annotations suivent les coordonnées de l'original.
- L'image originale est immuable : annotations courantes conservées en JSON, dans `Captures`, sans versionnage permanent.

### Fiche documentaire

- Affichée **en permanence à droite** (repli/redimensionnement envisageables en recette) : **Titre prérempli automatiquement**, **date/heure de capture en lecture seule**, **Description**, **Commentaire**, **0 ou 1 catégorie**, **plusieurs tags**.
- Tous les enrichissements sont **facultatifs** : cliquer simplement sur Enregistrer conserve la capture avec son titre proposé.
- Rouvrir une capture de la bibliothèque ramène dans **le même éditeur**, avec annotations et champs encore modifiables.

### Actions principales

- **Enregistrer / Ctrl + S** : conserver la capture et la fiche ; confirmation discrète, sans formulaire bloquant.
- **Exporter PNG/JPEG** ou **Copier vers le presse-papiers** : rendre **l'état affiché**, même non enregistré, **sans sauvegarder implicitement** dans la bibliothèque ; aucun suivi des fichiers exportés.
- **Naviguer, demander une autre capture ou quitter avec des modifications non sauvegardées** : montrer l'image avec le dialogue **Enregistrer / Ne pas enregistrer / Annuler** ; les trois choix restent distincts.
- **Suppression volontaire d'une capture archivée** : confirmation préalable. Ne pas confondre cette action avec « supprimer l'annotation sélectionnée ».

### Éléments visibles mais NON validés comme fonctionnalités V0

La maquette montre aussi **Surligner**, **Crayon**, **Flou**, **Masque**, certains contrôles de recadrage, des onglets supplémentaires ainsi que des icônes d'aide/paramètres. **Ne pas les implémenter automatiquement.** La V0 n'est pas un logiciel de retouche ni d'anonymisation. Garder la qualité de la composition visuelle, mais ne proposer que les outils du cahier des charges, sauf décision ultérieure.

Le titre d'exemple « Voyage – Glencoe.jpg », l'astérisque du champ Titre et la photographie sont illustratifs : **aucun import d'image** n'est autorisé et le titre est déjà rempli, sans saisie obligatoire.

## 4. G-04a — Bibliothèque : mosaïque (moitié haute)

![Référence G-04 : mosaïque en haut, liste en bas](<Bibliothèque Ma Capture en deux modes.png>)

### Apparence

- Fenêtre Bibliothèque du même univers clair violet/bleu que l'éditeur.
- Navigation à gauche : toutes les captures, filtre **À classer** et catégories ; le reste dépend du périmètre réellement validé.
- Haut : bascule **Mosaïque / Liste**, champ de recherche, filtre tags, tri.
- Corps : cartes rectangulaires avec **miniature annotée**, titre, date, catégorie éventuelle et tags affichés sans surcharge.

### Comportement

- Le mode mosaïque et le mode liste sont **deux présentations du même catalogue**, pas deux collections de données.
- Les miniatures montrent **les annotations du dernier enregistrement**, pas les modifications en cours non enregistrées dans l'éditeur.
- Double-clic ou ouverture de carte : rouvrir l'éditeur G-03 avec la fiche et les annotations modifiables.
- Recherche sur titre, description, commentaire ; filtres catégorie/tags ; tri date ou titre ; **À classer = aucune catégorie**.
- Un utilisateur peut créer une catégorie/un tag depuis la fiche, avec une gestion simple depuis la bibliothèque.

### Visuels purement illustratifs

- Les vignettes montrent des **exemples de contenus capturés**, pas une fonction d'import de photos, PDF ou autres images.
- Les chiffres des compteurs et exemples de catégories sont des **données fictives**.
- Les **étoiles Favoris**, l'arborescence hiérarchique et les éventuelles fonctions de masse ne sont **pas validées** par les exigences 001–005 : ne pas les ajouter sans arbitrage.

## 5. G-04b — Bibliothèque : liste (moitié basse)

*Référence : même fichier G-04, partie basse.*

### Apparence

- Même entête, même navigation et mêmes filtres que la mosaïque : seule **la présentation centrale** change.
- Table lisible : petite miniature, **Titre**, **Catégorie**, **Tags**, **Date**, **Dimensions**, **Taille**. Les colonnes secondaires peuvent être ajustées en recette.
- Ligne sélectionnée mise en évidence avec une nuance violette très légère ; tri visible dans les en-têtes concernés.

### Comportement

- Une capture sélectionnée est la même que dans la mosaïque ; ouverture dans l'éditeur G-03.
- Recherche et filtres partagés avec la mosaïque, sans modification des données lors du changement de vue.
- Renommer/reclasser passe par la fiche documentaire ; la suppression volontaire passe par une **confirmation**.
- Les cases de sélection multiple, les étoiles et menus contextuels représentés dans l'image n'impliquent **pas** automatiquement une fonction de traitement par lots : à arbitrer si besoin.

## 6. Parcours complet entre les écrans


```text
Démarrer Ma Capture
        ↓
G-01 — Splash / vérification des données
        ├── Travail provisoire récupérable ? → G-03 + dialogue de reprise
        └── Sinon → G-02 — Bandeau flottant en haut
                      ├── Plein écran / Rectangle / Fenêtre → G-03 — Éditeur
                      │                                      ├── Enregistrer → capture archivée, éditeur disponible
                      │                                      ├── Exporter / Copier → rendu de l'état visible
                      │                                      └── Modifier / reclasser → même écran
                      ├── Bibliothèque → G-04a Mosaïque ⇄ G-04b Liste
                      │                       └── Ouvrir capture → G-03
                      └── Fermer → Quitter
```

La nouvelle capture doit être accessible de manière simple après un enregistrement, sans système de « capture en rafale » ni deuxième bouton de sauvegarde.

## 7. Règles de réalisation et recette graphique

1. **Fidélité aux maquettes validées**, sans chercher à transformer une illustration en liste de nouvelles fonctionnalités.
2. **Même composants et navigation** entre Bibliothèque mosaïque/liste ; **même éditeur** lors de la création ou de la réouverture.
3. **Proportions souples** : image prioritaire dans l'éditeur, panneau documentaire utilisable, aucun champ essentiel masqué.
4. **États réels** : vide, capture non enregistrée, capture enregistrée, annotation sélectionnée, erreur d'enregistrement, récupération après plantage.
5. **Vérification manuelle sur Windows** : splash court, bandeau haut centré, prise de vue qui ne capture pas le bandeau, outils d'annotation utilisables, écran de bibliothèque clair et cohérent dans les deux vues.
6. **Pas de modification automatique de la spécification** pour « embellir » ou « enrichir » l'interface ; les désaccords entre illustration et périmètre sont remontés pour arbitrage.

## 8. Derniers arbitrages utiles avant de donner ce guide à Codex

- **Raccourci global de V0** : l'utilisateur veut un autre raccourci que celui de Windows ; **combinaison précise à choisir** et absence de conflit à vérifier. **Personnalisation en V2**.
- **Favoris et sélection multiple** dessinés dans la bibliothèque : simples éléments visuels ou véritables fonctionnalités ? Aucun ajout implicite.
- **Outils supplémentaires** dessinés dans l'éditeur (Flou, Masque, etc.) : **hors périmètre V0 sauf validation expresse**.
- **Disposition exacte** des commandes autour de l'éditeur et gestion des panneaux sur de petites résolutions : à valider lors de la recette graphique, sans changer les parcours.

**Pas de travail de développement avant validation finale du guide et découpage explicite des missions.**