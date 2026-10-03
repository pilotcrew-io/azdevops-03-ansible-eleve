# Sources primaires

Vérification documentaire : **3 octobre 2026**. Les pages évoluent ; revalider image, quota, options
Azure CLI et versions avant une nouvelle promotion. Aucun prix fixe n'est avancé.

| Source | Lien officiel | Usage dans l'atelier |
|---|---|---|
| Azure CLI — VM | https://learn.microsoft.com/en-us/cli/azure/vm?view=azure-cli-latest | vm create, clé SSH, NSG explicite, Run Command |
| Images VM | https://learn.microsoft.com/en-us/cli/azure/vm/image?view=azure-cli-latest | vérifier l'URN disponible avant création |
| NSG — règles | https://learn.microsoft.com/en-us/cli/azure/network/nsg/rule?view=azure-cli-latest | sources /32, ports et priorités |
| Linux VM CLI quickstart | https://learn.microsoft.com/en-us/azure/virtual-machines/linux/quick-create-cli | image Ubuntu et cycle de création |
| Azure CLI installation | https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-linux | préparer CLI |
| Ansible installation | https://docs.ansible.com/projects/ansible/latest/installation_guide/intro_installation.html | contrôleur et Python |
| Inventaires | https://docs.ansible.com/projects/ansible/latest/inventory_guide/intro_inventory.html | alias, groupes et variables de connexion |
| become | https://docs.ansible.com/projects/ansible/latest/playbook_guide/playbooks_privilege_escalation.html | élévation de privilèges |
| systemd_service | https://docs.ansible.com/projects/ansible/latest/collections/ansible/builtin/systemd_service_module.html | état service et daemon_reload |
| Handlers | https://docs.ansible.com/projects/ansible/latest/playbook_guide/playbooks_handlers.html | notification et flush |
| Check et diff | https://docs.ansible.com/projects/ansible/latest/playbook_guide/playbooks_checkmode.html | limite des simulations |
| Java17 HttpServer | https://docs.oracle.com/en/java/javase/17/docs/api/jdk.httpserver/com/sun/net/httpserver/HttpServer.html | serveur et cycle d'arrêt |
| Maven | https://maven.apache.org/guides/getting-started/ | structure standard et verify |
| systemd.service | https://raw.githubusercontent.com/systemd/systemd/main/man/systemd.service.xml | ExecStart, Restart, User |

Les prescriptions pédagogiques (durée, barème, nom des fichiers) sont des choix de ce support.
La disponibilité des ressources et leur coût doivent être constatés dans l'abonnement autorisé.

Le site de rendu freedesktop du manuel était indisponible lors de la vérification ;
la source XML officielle du projet systemd a été consultée à sa place.
