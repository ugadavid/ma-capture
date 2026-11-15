# Ma Capture

Application Windows locale dédiée à la **capture d’écran**, à son **annotation** et à la **gestion documentaire des captures réalisées par l’application**.

> **État du projet : conception fonctionnelle.** Aucune mission de développement n’a encore été lancée. Les documents expriment les décisions et les points à étudier ; ils ne constituent pas un ordre de coder.

## Dossier de conception

1. [001 — Vision et périmètre fonctionnel](docs/conception/001-vision-et-perimetre.md) — objectifs, parcours, fonctions incluses/exclues, principes confirmés et arbitrages ouverts.
2. [002 — Parcours utilisateurs et ergonomie](docs/conception/002-parcours-utilisateurs-et-ergonomie.md) — écrans, outils, raccourcis, bibliothèque et dialogue **Enregistrer / Ne pas enregistrer / Annuler**.
3. [003 — Architecture et modèle de données](docs/conception/003-architecture-et-modele-de-donnees.md) — deux bases SQLite, BLOB et SHA-256, annotations, fichier provisoire et contrôle de cohérence.
4. [004 — Intégrité, sauvegardes et cas limites](docs/conception/004-integrite-sauvegardes-et-cas-limites.md) — fichiers provisoires, reprise après plantage, contrôle des orphelins et sauvegarde/restauration ZIP.
5. [005 — Exigences et critères de validation](docs/conception/005-exigences-et-criteres-de-validation.md) — exigences V0, critères observables et plans de recette UX, intégrité et décisions finales.

## Référence graphique et interactive

**Pour reproduire l'interface dans l'application Windows native :**

- [Prototype HTML interactif de référence](docs/graphisme/ma-capture-prototype.html) — disposition, style, navigation et interactions visibles. **Le JavaScript est une simulation, pas la logique native à copier.**
- [Guide des écrans pour Codex](docs/graphisme/guide-des-ecrans-ma-capture.md) — ordre des écrans, comportements, limites des maquettes et référence des popups.
- Captures du même prototype : [01-splash](docs/graphisme/01-splash.png) · [02-bandeau](docs/graphisme/02-bandeau.png) · [03-editeur](docs/graphisme/03-editeur.png) · [03-editeur-popup](docs/graphisme/03-editeur-popup.png) · [04-mosaique](docs/graphisme/04-bibliotheque-mosaique.png) · [05-liste](docs/graphisme/05-bibliotheque-liste.png).

**Priorité en cas de divergence :** documents de conception pour le comportement et les données ; prototype HTML pour le design et les interactions visuelles ; PNG pour les états graphiques. Pas d'ajout de fonction à partir d'un détail illustratif.

**V0 décidée :** Favoris par étoile dans les deux vues, raccourci global `Ctrl + Alt + C`, popups applicatifs cohérents avec `03-editeur-popup.png`. **Pas de sélection multiple ni de traitement en masse en V0.**

**Relecture transversale :** [cohérence des cinq documents et registre des derniers détails de réalisation](docs/conception/relecture-transversale-et-points-ouverts.md). Les décisions principales sont harmonisées ; les détails ouverts sont classés par priorité, sans lancer de développement.

**Principe directeur :** capturer et annoter sans être obligé de tout documenter immédiatement ; pouvoir compléter la fiche plus tard. Pas de gestionnaire universel d’images, pas d’usine à gaz.
