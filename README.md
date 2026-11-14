# Ma Capture

Application Windows locale dédiée à la **capture d’écran**, à son **annotation** et à la **gestion documentaire des captures réalisées par l’application**.

> **État du projet : conception fonctionnelle.** Aucune mission de développement n’a encore été lancée. Les documents expriment les décisions et les points à étudier ; ils ne constituent pas un ordre de coder.

## Dossier de conception

1. [001 — Vision et périmètre fonctionnel](docs/conception/001-vision-et-perimetre.md) — objectifs, parcours, fonctions incluses/exclues, principes confirmés et arbitrages ouverts.
2. [002 — Parcours utilisateurs et ergonomie](docs/conception/002-parcours-utilisateurs-et-ergonomie.md) — écrans, outils, raccourcis, bibliothèque et dialogue **Enregistrer / Ne pas enregistrer / Annuler**.
3. [003 — Architecture et modèle de données](docs/conception/003-architecture-et-modele-de-donnees.md) — deux bases SQLite, BLOB et SHA-256, annotations, fichier provisoire et contrôle de cohérence.
4. [004 — Intégrité, sauvegardes et cas limites](docs/conception/004-integrite-sauvegardes-et-cas-limites.md) — fichiers provisoires, reprise après plantage, contrôle des orphelins et sauvegarde/restauration ZIP.
5. [005 — Exigences et critères de validation](docs/conception/005-exigences-et-criteres-de-validation.md) — exigences V0, critères observables et plans de recette UX, intégrité et décisions finales.

**Relecture transversale :** [cohérence des cinq documents et registre des points restant à trancher](docs/conception/relecture-transversale-et-points-ouverts.md). Les décisions principales sont harmonisées ; les détails ouverts sont classés par priorité, sans lancer de développement.

**Principe directeur :** capturer et annoter sans être obligé de tout documenter immédiatement ; pouvoir compléter la fiche plus tard. Pas de gestionnaire universel d’images, pas d’usine à gaz.
