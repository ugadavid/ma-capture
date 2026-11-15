# AGENTS.md — Règles permanentes de Ma Capture

**Portée :** tout le dépôt `ugadavid/ma-capture`, toutes les missions de développement Codex et **leur préparation par ChatGPT**.  
**État de référence :** conception V0 terminée, développement non lancé au 15 novembre 2026.  
**À lire avant toute mission.** Une instruction de mission précise le travail du jour ; elle ne supprime pas les règles permanentes ci-dessous sans décision explicite de David.

## 1. RÈGLE PRIORITAIRE — Alpha expérimentale : données jetables, ZÉRO migration

**Tant que David n'a pas décidé explicitement et sans ambiguïté de commencer à conserver des données réelles, Ma Capture est un projet en alpha expérimentale dont les données applicatives sont des données de TEST JETABLES.**

Conséquences obligatoires :

1. **AUCUNE MIGRATION DE BASE DE DONNÉES.** Ne pas créer de script de migration, de moteur de migrations, d'historique de schémas, de conversion d'anciennes données, de logique de compatibilité ascendante ou de procédure de mise à niveau d'une ancienne base de test. Ne pas en proposer « par prudence », « pour préparer la production » ni « au cas où ».
2. **Schéma modifié = bases de TEST supprimées puis recréées.** Mettre à jour directement la définition canonique du schéma et, si nécessaire, supprimer/recréer `catalogue.db` **et** `images.db` dans l'environnement de développement ou de recette identifié. Repartir avec des données de démonstration neuves, sans conversion, migration ni sauvegarde préalable obligatoire.
3. **Perdre des données de test est acceptable.** Les captures, annotations, catégories, tags, favoris, fichiers provisoires et bases **créés pour le développement/la recette** peuvent être effacés et reconstitués sans scrupule et sans solliciter David à chaque réinitialisation. Ne pas investir du temps dans la préservation de ces données.
4. **Aucun seuil automatique.** Ni la mission MC-014, ni MC-225, ni le nombre d'utilisateurs fictifs, ni la richesse du logiciel, ni une version alpha avancée ne changent cette règle. **Seul David déclenche explicitement le passage à un régime de données à préserver.** Ce changement devra alors faire l'objet d'une révision expresse de ce fichier et d'une décision séparée.
5. **Cette interdiction s'adresse aussi à ChatGPT qui rédige les missions.** Les briefs ne doivent **ni exiger, ni recommander, ni suggérer en option** une migration ou une compatibilité avec les anciens schémas de test. Si une mission future contient un tel mécanisme, la mission est à corriger **avant** d'être confiée à Codex.

**Frontière impérative :** cette permission de jeter concerne **uniquement les données applicatives de test identifiées comme telles**. Elle n'autorise jamais à supprimer des sources Git, des modifications de travail, des maquettes, des rapports, des documents, des fichiers personnels, des données d'autres applications ou une base dont le caractère jetable est incertain. Dans le doute sur la cible, identifier l'emplacement avant toute suppression. Ne publier aucune capture contenant des informations personnelles.

**Ne pas confondre :** la V0 doit **implémenter et tester** les comportements fonctionnels de sauvegarde, de confirmation, de reprise après plantage, de contrôle de cohérence et de restauration décrits dans les documents 003–005. Cela se fait avec **des données de test recréables**. Ces fonctionnalités attendues du produit ne constituent **pas** une obligation de migrer ou de conserver les jeux de données utilisés pendant le développement.

> **Phrase de contrôle :** « Les bases de test sont incompatibles ? On les jette, on recrée le schéma courant, on recommence les tests. Aucune migration. »

## 2. Versionnement volontairement lent — baby steps

- La première version applicative initiale s'appelle **`alpha-0.1.0`** ; ensuite, conserver la série **`alpha-0.1.1`, `alpha-0.1.2`, …, `alpha-0.1.n`** aussi longtemps que David le souhaite.
- **Une nouvelle mission n'impose pas une nouvelle version.** Incrémenter uniquement la dernière composante lorsqu'une étape applicative publiable le justifie ; un commit documentaire, un ajustement interne ou un rapport ne provoque pas automatiquement un incrément.
- **Jamais de promotion automatique** en `alpha-0.2.0`, bêta, `1.0.0` ou autre palier. Seul David peut décider d'un changement de niveau.
- Maintenir **une source canonique unique** pour la version applicative lorsque le code correspondant existe ; les affichages et artefacts de build utilisent cette source. Ne pas dupliquer manuellement la version partout.
- La version du **prototype HTML de référence**, s'il en existe une dans son historique, **n'est pas** une version de l'application Windows.

## 3. Philosophie : petite application, pas une centrale nucléaire

Ma Capture est une petite application **Windows, locale, mono-utilisateur et à instance unique**. Son métier : capturer, annoter, documenter, retrouver et exporter **ses propres captures d'écran**.

- **Simplicité avant abstraction**, composants existants avant réinvention, code lisible avant ingénierie spectaculaire.
- Ne pas construire « pour la NASA » : aucun framework interne, gestionnaire universel de données, service, microservice, bus de messages, moteur générique de migration, système de plugins, infrastructure de déploiement ou système de récupération élaboré sans besoin validé.
- Les tests, journaux, rapports, configurations, bibliothèques et contrôles doivent être **proportionnés au risque réel**. Ne pas ajouter de rituels techniques uniquement pour paraître robuste.
- Ne pas élargir silencieusement une mission ; ne pas refactoriser des parties voisines « tant qu'on y est ».
- **Réutiliser les composants communs** : logo, palette, boutons, champs, dialogs, navigation, rendu des annotations et accès aux bases ; ne pas multiplier les implémentations parallèles.

## 4. Autorités, documents de référence et limites

Pour comprendre **ce que doit faire le produit**, consulter les documents :

- `docs/conception/001-vision-et-perimetre.md` — vision et exclusions ;
- `docs/conception/002-parcours-utilisateurs-et-ergonomie.md` — parcours et dialogues ;
- `docs/conception/003-architecture-et-modele-de-donnees.md` — deux SQLite et état des annotations ;
- `docs/conception/004-integrite-sauvegardes-et-cas-limites.md` — comportement de protection **dans l'application** ;
- `docs/conception/005-exigences-et-criteres-de-validation.md` — critères de réception.

Pour comprendre **l'interface à reproduire**, consulter `docs/graphisme/ma-capture-prototype.html`, `docs/graphisme/guide-des-ecrans-ma-capture.md` et les **six PNG** associés, dont `03-editeur-popup.png`. Le HTML est une **maquette interactive**, pas un moteur JavaScript de capture à transposer. Les exemples d'images sont fictifs.

Pour connaître le **découpage prévisionnel**, consulter `docs/planification/feuille-de-route-v0.md`. Cette feuille de route **n'autorise à elle seule aucune mission**.

**Hiérarchie :** les instructions d'environnement applicables et la mission expressément lancée délimitent l'action ; `AGENTS.md` impose les règles de travail permanentes ; les documents 001–005 fixent le métier ; le HTML/PNG fixent la cible graphique. En cas de divergence substantielle, **signaler précisément le conflit** au lieu de décider seul un changement de produit.

Seul David arbitre le périmètre produit, les changements majeurs de solution, une promotion de version et **le passage à des données non jetables**. Les choix techniques locaux compatibles avec la mission sont délégués à Codex.

## 5. Vérifications avant et pendant une mission

1. Identifier le dépôt et la branche ; inspecter `git status` et les modifications préexistantes. **Ne rien effacer pour obtenir artificiellement un dépôt propre.**
2. Lire `AGENTS.md`, la mission confiée et **les seules références réellement utiles** au travail du jour. Vérifier l'état courant du code avant de s'appuyer sur un ancien rapport.
3. Réaliser **un objectif principal**, un diff limité, et respecter les interfaces/contrats déjà en place.
4. Les défaillances ou idées hors périmètre sont signalées ; elles ne deviennent pas spontanément des travaux.
5. Pour une modification des bases, utiliser **le schéma courant et des données jetables**, sans migration, conformément à la règle 1.
6. Si une difficulté impose une décision importante de David, isoler le point bloqué ; poursuivre seulement les parties indépendantes et sûres.

## 6. Contraintes de produit à ne pas réinventer

- **Deux bases SQLite indépendantes** : `catalogue.db` pour le métier et `images.db` pour les BLOB/SHA-256. Une capture = une image. Identifiants entiers, pas de GUID/UUID ; pas de transaction interbase distribuée ni de `ATTACH`.
- **Original conservé intact** ; annotations courantes en JSON dans `Captures`, aucun historique permanent de versions. Favoris V0 par booléen ; **aucune sélection multiple**.
- **Pas d'import d'images ni de collage entrant** ; pas d'OCR, de flou/masque, de retouche avancée, de cloud ou de service.
- **Export PNG/JPEG et copie sortante** sans historisation des exports. Les miniatures reflètent la **dernière sauvegarde**, non les modifications non enregistrées.
- **Dialogue Enregistrer / Ne pas enregistrer / Annuler** pour les risques de perte en usage réel ; les popups applicatifs doivent respecter la référence graphique. Le raccourci global V0 est `Ctrl + Alt + C`.

Pour le détail, **ne pas recopier ou remplacer les spécifications** : suivre les documents cités en section 4.

## 7. Tests, preuves, rapports et Git

- Tester les changements selon les critères pertinents de `005` : tests ciblés, vérifications du schéma, vrais essais Windows quand nécessaire. **Ne pas confondre build réussi, tests automatisés réussis et recette graphique/interactive acceptée par David.**
- Des tests de perte et de récupération de données se font **sur des bases de test que l'on peut détruire**. Aucune suite de tests de compatibilité/migration avec d'anciens schémas n'est requise pendant l'alpha jetable.
- Consigner les décisions durables dans les documents canoniques ; produire un rapport **proportionné** lorsqu'une mission significative mérite une trace, avec résultats réellement observés, limites et actions restantes.
- Avant clôture : vérifier le diff, les changements préexistants, les tests effectués et l'état Git. **Jamais de push forcé, jamais de réécriture d'historique, jamais d'écrasement du travail concurrent.**
- Une publication sur `origin/main` doit être **autorisée par la mission/protocole effectivement adopté**. Si autorisée, publier et vérifier le SHA distant, y compris en cas de résultat partiel ou négatif utile.
- Après restitution, **s'arrêter** : ne pas démarrer spontanément la mission suivante.

## 8. Règle spéciale pour la rédaction des missions par ChatGPT

**Avant chaque nouvelle mission Ma Capture, appliquer les sections 1 et 2 à la lettre.** Les missions décrivent **le prochain petit changement à réaliser**, pas une infrastructure future hypothétique.

**Interdit**, même sous forme de recommandation « facultative » tant que le statut alpha jetable n'a pas été révoqué par David : créer un plan de migration SQLite, préserver de vieux schémas de test, convertir des bases existantes, imposer un retour arrière des données de test ou demander une sauvegarde de précaution pour chaque réinitialisation de développement.

**Autorisé et attendu :** proposer de **supprimer et recréer les deux bases de test** lorsqu'un schéma change ; construire progressivement les **fonctionnalités métier** de sauvegarde, récupération et restauration en s'appuyant sur des jeux de test recréables.

**Rappel final : la mission 225 n'est pas un seuil. La décision de commencer à protéger les données réelles appartient exclusivement à David.**
