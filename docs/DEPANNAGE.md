# Dépannage raisonné

Ne répétez pas une création cloud pour corriger un problème de configuration. Commencer par lire
l'erreur exacte, puis comparer les couches. Garder les preuves privées quand elles contiennent des IP.

| Symptôme | Vérification ciblée | Cause plausible et correction |
|---|---|---|
| `az` vise le mauvais groupe | `az account show`, nom et tags du groupe | mauvais abonnement ; sélectionner seulement celui autorisé |
| SKU/quota/image refusé | message Azure, région, quotas approuvés | faire valider une taille/région, ne pas créer des VM en boucle |
| timeout SSH | état VM, IP publique, NSG effectif, IPv4 VPN | source /32 devenue fausse ou VM arrêtée ; corriger la règle prévue |
| `Permission denied (publickey)` | user, `ssh -v`, clé chargée | mauvais utilisateur/clé ; garder le mot de passe désactivé |
| empreinte hôte changée | Run Command + empreinte ssh-keygen | VM recréée ou interception ; vérifier avant de modifier known_hosts |
| `UNREACHABLE` Ansible | SSH direct puis Python3 distant | inventaire erroné ou interpréteur absent |
| `sudo` demande un mot de passe | `sudo -n true` distant | droits du compte insuffisants ; adapter via l'administrateur, pas via un secret dans Git |
| source JAR introuvable | chemin local absolu, `mvn verify` | compilation absente ; produire l'artefact avant le playbook |
| `status=203/EXEC` | unité et `/usr/bin/java` | chemin ou permission ExecStart incorrect |
| service échoue immédiatement | `journalctl -u academy -n 50 --no-pager` | JAR/cfg invalide ou port occupé ; vérifier avant redémarrage |
| santé VM OK, poste KO | IP sortie et règles TCP8080 | réseau/NSG, pas défaut Java |
| deuxième passage changed | tâche nommée dans recap détaillé | cache apt expiré, valeurs fluctuantes ou restarted permanent |
| check sur VM vierge échoue | existence utilisateur/fichier | prévision dépend d'un état non créé ; syntax-check puis vrai déploiement autorisé |
| fichiers d'unité changés sans effet | notifications, ordre handlers | manque daemon_reload/flush, puis santé après redémarrage |

## Lecture opérationnelle

```bash
sudo systemctl status academy --no-pager
sudo systemctl show academy -p MainPID -p User -p ActiveState
sudo journalctl -u academy -n 50 --no-pager
ss -lnt
curl --fail http://127.0.0.1:8080/health
```

Ces commandes lisent l'état. Tester ensuite l'URL publique depuis votre poste. Une réponse 404 est
un signe que le réseau mène à un serveur, mais ne prouve pas que la route attendue fonctionne.
Si le groupe est partiellement créé, lire uniquement ses ressources et reprendre la tâche manquante
sous contrôle du formateur ; le script de référence refuse délibérément un groupe préexistant.
