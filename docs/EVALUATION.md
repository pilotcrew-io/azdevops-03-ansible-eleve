# Évaluation — 20 points

Évaluer des résultats réellement observés, liés au commit rendu. L'enseignant lit les fichiers et
les preuves, puis demande au participant d'expliquer un choix. Le corrigé n'est pas une preuve.

| Critère | Points | Preuve attendue et attribution |
|---|---:|---|
| Application autonome | 3 | 1 JAR Java17 exécutable, 1 tests santé/404/405, 1 SHA et contrat documentés |
| VM et périmètre réseau | 4 | 1 groupe/tag/propriétaire, 1 Ubuntu + clé, 1 SSH limité /32, 1 HTTP limité /32 et vérification d'empreinte |
| Inventaire et privilèges | 2 | 1 inventaire privé/ping, 1 distinction SSH/become/compte Java |
| Rôle et systemd | 4 | 1 modules déclaratifs, 1 utilisateur et fichiers, 1 templates/variables validées, 1 service enabled/active |
| Idempotence et handlers | 4 | 2 second passage changed=0 et PID stable, 1 modification contrôlée/handler, 1 check/diff expliqué |
| Exploitation et nettoyage | 3 | 1 santé locale/externe et journaux, 1 incident raisonné, 1 suppression RG vérifiée |
| **Total** | **20** | |

Rendre : URL/commit du fork, réponses complétées, commande et recap de trois passages, PID avant/après,
SHA du JAR, réponses HTTP, règles NSG masquées, compte Java actif, état final du groupe. Les journaux
peuvent être copiés en texte avec date et contexte. Retirer identifiants et IP d'une version publique.

Les 4 points VM/réseau nécessitent Azure ; une VM locale ne les remplace pas. Un atelier local permet
une évaluation provisoire des autres éléments, en indiquant les limites. Pour un critère non exécuté,
mettre « non vérifié » et réserver les points jusqu'à démonstration. Une preuve inventée ne vaut aucun
point. Un secret commité doit être révoqué et l'incident résolu avant toute diffusion du dépôt.
