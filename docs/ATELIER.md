# Atelier 03 — de l'inventaire à un service exploitable

**260 minutes**, dont 10 minutes de pause. Les prérequis sont réalisés avant la séance.
Vous travaillez individuellement, puis comparez les preuves avec un binôme. Chaque mission impose
un résultat et des contrôles ; les choix et fichiers de réalisation vous appartiennent.

## Situation et contrat

Une équipe reçoit une application HTTP Java 17 et doit la déployer de manière répétable dans une
VM Ubuntu Azure. Vous créez ici une petite application autonome : Maven construit un JAR exécutable,
`GET /health` renvoie 200 et `ok`, `/version` affiche une version, `/` un message configurable.
Une route inconnue renvoie 404 ; une méthode autre que GET renvoie 405. Écouter sur une adresse
accessible depuis la VM et un port non privilégié, par défaut 8080. Aucun atelier précédent requis.

Le service `academy` s'exécute sous un utilisateur système dédié, sans shell de connexion, depuis
`/opt/academy/app.jar`. Une unité systemd assure le redémarrage en cas d'échec et le démarrage au boot.
Ansible installe Java, crée le compte et les répertoires, dépose le JAR, rend les templates d'unité et
configuration, gère le service et vérifie la santé. L'inventaire Azure est privé et exclu de Git.

| Bloc | Durée | Fin cumulée | Production / preuve |
|---|---:|---:|---|
| 1. Contrat Java et construction | 35 min | 35 | JAR autonome, tests locaux |
| 2. VM, réseau et confiance SSH | 40 min | 75 | groupe dédié, NSG, empreinte vérifiée |
| 3. Inventaire et privilèges | 30 min | 105 | ping Ansible, facts Ubuntu, become |
| 4. Rôle et premier déploiement | 50 min | 155 | arborescence rôle, service et santé |
| Pause | 10 min | 165 | |
| 5. Idempotence et changement maîtrisé | 40 min | 205 | deuxième passage, handler, check/diff |
| 6. Exploitation et diagnostic | 30 min | 235 | incident résolu, journaux, serial |
| 7. Preuves, évaluation et nettoyage | 25 min | 260 | dossier, suppression vérifiée |

## Mission 1 — Construire un artefact observable (35 min)

Créer `app/pom.xml`, les sources sous `app/src/main/java/` et les tests sous
`app/src/test/java/`. Écrire un point d'entrée Java et des tests dans les chemins Maven standards. Fixer Java17,
un nom de JAR et un manifeste Main-Class. Fournir les routes du contrat. La variable d'environnement
APP_MESSAGE règle le message ; APP_VERSION expose la version ; le port est configurable. Un test
vérifie au moins la santé, la route inexistante et la méthode interdite. Éviter une dépendance sur une
base ou un autre service. Documenter comment arrêter proprement le processus.

```bash
mvn --batch-mode -f app/pom.xml clean verify
jar tf app/target/app.jar
java -jar app/target/app.jar
# Dans un second terminal, puis interrompre Java avec Ctrl+C.
curl --fail http://127.0.0.1:8080/health
```

Acceptation : tests verts, JAR qui démarre avec Java17, `/health` donne exactement `ok` (saut de ligne
accepté). Consigner la version et l'empreinte SHA-256 de l'artefact ; ne pas versionner `target/`.
Question : pourquoi déployer le JAR construit plutôt que compiler sur la VM ?

## Mission 2 — Créer un périmètre Azure et établir SSH (40 min)

Définir les noms, région, propriétaire et IPv4 avant toute commande mutante. Avec l'aide-mémoire
Azure CLI et ses références, créer uniquement un groupe dédié `rg-azd03-<identifiant>` tagué
`workshop=azd03` et `owner=<identifiant>`, une VM Ubuntu22.04, son réseau et son IP publique Standard.
La VM doit utiliser la clé publique prévue, une petite taille autorisée et l'utilisateur `azureuser`.
Ne pas ouvrir de règle SSH implicite à Internet : créer le NSG explicitement et l'associer à la VM.
Deux règles entrantes autorisent votre seule IPv4 `/32`, TCP22 et TCP8080. Aucun accès 0.0.0.0/0.

Lire les règles **effectives** et contrôler les sources, ports et priorités. Lire l'empreinte de la
clé hôte depuis un canal Azure authentifié (Run Command dans le portail ou CLI). Comparer cette
empreinte à celle affichée par la première connexion SSH avant de l'accepter.

```bash
ssh -i ~/.ssh/azd03_ed25519 azureuser@IP_VM
whoami
python3 --version
sudo -n true
```

Acceptation : connexion par clé, sudo autorisé, NSG restreint. En cas de nouvelle IPv4 VPN, modifier
uniquement les règles prévues. Faire une capture masquée de leur lecture, pas un secret.

## Mission 3 — Séparer connexion et configuration (30 min)

Créer `ansible.cfg`, `inventory/azure.local.yml` (ignoré), `site.yml`, `requirements.txt` et l'ossature
`roles/academy/{defaults,tasks,handlers,templates}`. L'inventaire contient un groupe `web`, un alias
stable, ansible_host, ansible_user et le chemin local de la clé. Le playbook cible `web`, collecte les
facts, utilise become pour gérer le système et limite le traitement à un hôte à la fois (`serial`).

```bash
ansible-inventory -i inventory/azure.local.yml --graph
ansible web -i inventory/azure.local.yml -m ansible.builtin.ping
ansible web -i inventory/azure.local.yml -b -m ansible.builtin.command -a 'id'
ansible-playbook -i inventory/azure.local.yml site.yml --syntax-check
```

Acceptation : ping pong, `id` prouve les privilèges nécessaires et l'inventaire ne figure pas dans
`git ls-files`. Expliquer pourquoi le compte SSH et le compte d'exécution Java doivent être distincts.

## Mission 4 — Déclarer l'état désiré dans un rôle (50 min)

Utiliser des modules Ansible FQCN plutôt qu'un shell qui exécute apt/cp/systemctl. Des variables par
defaut fixent le port, le message et le chemin local du JAR. Installer le JRE17, créer l'utilisateur
sans shell, déposer le JAR propriétaire du compte, rendre l'unité et un fichier EnvironmentFile.
Valider les variables pour éviter les retours à la ligne injectés dans le fichier d'environnement.
Les changements de JAR ou configuration notifient un handler de redémarrage ; un changement d'unité
recharge systemd avant de redémarrer. Vérifier le service actif puis `/health` depuis la VM.

Acceptation : premier passage réussi, service enabled/active, Java exécuté par le compte dédié,
HTTP accessible depuis votre IPv4, aucun root pour le processus applicatif. Capturer PLAY RECAP,
un extrait d'unité et une réponse HTTP. Expliquer l'intérêt d'un handler par rapport à une tâche
`state: restarted` exécutée à chaque passage.

## Mission 5 — Démontrer l'idempotence (40 min)

Relancer immédiatement la même commande avec le même artefact et les mêmes variables. Obtenir
`changed=0` et aucun redémarrage. Relever le PID avant/après et les raisons possibles d'une variation
(cache apt expiré, nouvel artefact, variables modifiées). Sur une VM déjà déployée, exécuter
`--check --diff` et interpréter la prévision sans prétendre qu'elle prouve l'exécution réelle.
Modifier le message, puis déployer : seul le changement attendu et son handler doivent intervenir.
Rejouer une troisième fois pour obtenir `changed=0` avec la nouvelle configuration.

```bash
ansible-playbook -i inventory/azure.local.yml site.yml --check --diff
```

Acceptation : trois preuves distinctes, comparaison du PID, message réellement visible. Un premier
check sur VM vierge n'est pas un déploiement : les utilisateurs/répertoires prévus peuvent ne pas
exister pour les tâches suivantes. N'exposer aucun secret dans diff.

## Mission 6 — Faire fonctionner et diagnostiquer (30 min)

Dans la VM, arrêter volontairement le service puis prouver que le prochain passage rétablit l'état
started. Examiner `systemctl status`, `journalctl -u academy` et les ports d'écoute. Traiter un
incident limité (IP VPN changée OU chemin de JAR erroné), consigner hypothèse et preuve. Vérifier la
santé depuis la VM, puis depuis le poste pour distinguer application et réseau.
Expliquer `serial: 1` : sur plusieurs VM, finir la santé de l'une avant de poursuivre. Ici une seule
VM est créée ; `serial` n'apporte ni haute disponibilité ni absence d'interruption du redémarrage.
Ne pas créer une deuxième VM dans le budget de base. Extension facultative : concevoir l'inventaire
à deux hôtes et un équilibrage, ou la provision Terraform, après l'évaluation.

## Mission 7 — Rendre et supprimer (25 min)

Compléter le dossier de réponses, relire EVALUATION et attribuer des points avec le binôme. Vérifier
le nom et les tags du groupe dédié, énumérer ses ressources, confirmer le nom puis supprimer
**uniquement ce groupe**. Vérifier `az group exists` : `false` attendu une fois la suppression finie.
Supprimer la clé locale si elle ne sert plus ; ne pas supprimer une clé personnelle existante.
En cas de suppression partielle, consigner le blocage et demander au formateur de terminer ; ne pas
déclarer le nettoyage terminé avant la vérification.
