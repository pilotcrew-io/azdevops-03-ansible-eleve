# Atelier 03 — Configurer et exploiter une VM Azure avec Ansible

**Durée : 260 minutes (4 h 20), pauses comprises.** Prérequis à réaliser avant la séance.
Public : développeurs débutants ayant les bases Git, terminal et HTTP. Cet atelier est autonome ;
il ne dépend d'aucun autre atelier ni d'un ancien artefact.

Édition **élève — publique** du dépôt
`pilotcrew-io/azdevops-03-ansible-eleve`. Les noms désignent les dépôts cibles ; ce livrable ne les publie pas.

## Parcours

1. [Préparer son poste et ses accès](docs/PREREQUIS.md) avant le cours.
2. [Réaliser les missions chronométrées](docs/ATELIER.md).
3. [Comprendre les mécanismes](docs/MEMOS.md) et [diagnostiquer](docs/DEPANNAGE.md).
4. [Constituer les preuves et consulter le barème sur 20](docs/EVALUATION.md).
5. [Sources primaires et date de vérification](docs/SOURCES.md).
6. Remplir [le dossier de réponses](templates/REPONSES.md) ; créer soi-même les fichiers demandés.

L'accès Azure autorisé, le budget et les ressources disponibles sont des **conditions préalables**.
Les ressources cloud ne sont pas gratuites par principe. Une validation locale prépare et vérifie des
éléments du travail ; elle ne remplace pas les preuves Azure et ne démontre aucun provisionnement.
Les commandes de création fournies au corrigé ne sont pas exécutées dans le cadre de la préparation de ce support.

## Livrable attendu

Un JAR Java 17 répondant à `/health`, déployé par un rôle Ansible dans une VM Ubuntu Azure, exécuté sous systemd avec un compte dédié ; SSH et HTTP limités à votre IPv4 ; deux passages identiques sans changement au second.

Consulter [SECURITY.md](SECURITY.md) avant de créer une ressource ou de diffuser des preuves.
Licence MIT — © 2026 Curtys ACCIPE.

## Parcours GitHub et remise

Lire [CONTRIBUTING.md](CONTRIBUTING.md) pour le fork, la branche, la pull request et les preuves attendues.
