# Mémos — Ansible et exploitation

## Contrôleur, inventaire, hôte géré

Ansible s'exécute sur votre poste et contacte la VM par SSH. L'alias d'inventaire est un nom logique ;
ansible_host fournit l'adresse réelle. `ansible_user` répond à « qui se connecte ? » ; `become: true`
répond à « avec quels droits cette tâche agit-elle ? ». L'utilisateur applicatif répond encore à une
autre question : « qui exécute le Java en production ? ». Les trois identités ne se confondent pas.
Les facts décrivent le système reçu, ils ne garantissent pas son état futur. `ping` est un test du
module Python via SSH, pas un ping ICMP. Garder le contrôle des clés hôtes ; une nouvelle empreinte
peut être légitime après recréation mais doit être vérifiée par le canal Azure authentifié.

## État et idempotence

`package: state=present` exprime une installation ; un shell `apt install` oblige à interpréter une
commande. `copy` compare les contenus, `template` compare le rendu, `systemd_service: started`
laisse un service actif tranquille. `restarted` le redémarre toujours. Un rôle regroupe des tâches,
variables, templates et handlers autour d'une responsabilité. Les defaults sont les valeurs les
moins prioritaires ; l'inventaire ou `--extra-vars` peut les remplacer. Documenter les overrides.
Une preuve d'idempotence compare deux passages dans les mêmes conditions. `changed=0` signifie
qu'Ansible n'a rien déclaré changé dans ce passage ; cela ne garantit ni absence de dérive invisible,
ni santé métier sans contrôle dédié. Une actualisation du cache apt peut compter comme changement.

## Templates et notifications

Jinja2 remplace des variables dans un texte ; il ne valide pas la sémantique systemd. Le rôle doit
contraindre port et valeurs d'environnement. Un handler est une tâche déclenchée par une notification
lorsqu'une tâche a changé ; plusieurs notifications ne provoquent qu'une exécution du même handler
au point de traitement. L'ordre de définition des handlers compte. Recharger le gestionnaire après
modification de l'unité, puis redémarrer, puis vérifier la santé. `meta: flush_handlers` réalise ces
actions avant le contrôle HTTP au lieu d'attendre la fin du play.

## Check, diff et vérification réelle

`--syntax-check` analyse la structure et la résolution des rôles sans exécuter les tâches.
`--check` prédit les changements pour les modules compatibles, sans créer l'état prévu. Des tâches
qui dépendent d'un compte ou fichier encore inexistant peuvent échouer ; l'utiliser d'abord après
un déploiement. `--diff` montre les contenus avant/après pour les modules compatibles et peut exposer
un secret. Un scénario local de rendu ou compilation ne démontre ni SSH Azure ni systemd distant.
Pour l'acceptation, vérifier le port local à la VM, le service, les journaux et l'accès externe.

## Réseau Azure et SSH

Le NSG autorise ou refuse des flux selon priorité, source, destination, protocole et port. Les règles
par défaut ne remplacent pas l'autorisation HTTP/SSH choisie. Une source IPv4/32 désigne une seule
adresse de sortie, souvent celle du VPN/NAT. Ne pas diagnostiquer une application avant de comparer
le test VM-local au test externe. Une IP publique Standard est une ressource facturable distincte.
Un mot de passe désactivé et une clé forte n'autorisent pas à ouvrir SSH à tout Internet. L'empreinte
hôte authentifie la machine, tandis que votre clé utilisateur authentifie le client.

## systemd et livraison

Une unité décrit ExecStart, User, EnvironmentFile, Restart et le démarrage au boot. Le JAR reste un
artefact construit une fois puis copié ; le chemin source est local au contrôleur, dest distant.
`NoNewPrivileges`, `PrivateTmp` et les protections de chemins réduisent les permissions du service.
La santé HTTP représente un contrôle minimal : elle ne mesure pas la charge, les dépendances ni un
SLA. `serial: 1` borne le lot ; sans équilibrage, le redémarrage d'une VM interrompt son service.
Conserver les versions et SHA des JAR précédents permet une restauration explicite, suivie de santé.
