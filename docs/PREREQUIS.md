# Prérequis — à terminer avant les 260 minutes

## Poste

Linux Ubuntu 22.04/24.04 ou WSL2 Ubuntu ; macOS peut contrôler Ansible mais nécessite des outils
équivalents. Windows natif n'est pas le nœud de contrôle de cet atelier. Installer Git, OpenSSH,
Python 3.11/3.12, un JDK 17, Maven 3.9.x et Azure CLI selon les liens officiels de SOURCES.
Le navigateur doit pouvoir accéder à GitHub et au portail Azure. Le contrôleur a besoin de Python ;
la VM Ubuntu fournit Python 3 pour les modules Ansible. Utiliser un environnement Python isolé.

Commandes de préparation autorisées, qui ne créent pas de ressources cloud :

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install 'ansible-core==2.19.3'
git --version
ssh -V
java -version
mvn -version
ansible --version
az version
```

Confirmer que Java indique 17 et que Maven utilise ce même JDK. Pour changer le JDK, régler
JAVA_HOME selon l'installation locale, puis rouvrir le terminal. Ne pas installer Ansible en root.
Le verrou pédagogique 2.19.3 donne un comportement reproductible ; contrôler les avis de sécurité
Ansible avant une utilisation hors atelier et tester toute mise à jour.

## Accès Azure et budget

Un abonnement Azure actif est indispensable au parcours cloud. Le formateur valide avant la séance
la région, le quota VM (une Standard_B1s), la disponibilité de l'image Ubuntu et le coût accepté.
Une autre petite taille approuvée est possible si B1s est indisponible ; la tarification varie.
Le participant peut créer un groupe dédié, ou un administrateur le prépare et lui accorde les droits
nécessaires uniquement sur ce groupe. Les commandes de référence créent un nouveau groupe : en cas
de groupe préparé, suivre la variante explicitée au corrigé, sans retirer les garde-fous.
Il doit pouvoir gérer réseau, VM et Run Command dans ce groupe. Pas de compte technique Contributor
sur tout l'abonnement. Aucun déploiement n'est effectué par ce document.

```bash
az login
az account show --query '{subscription:name,id:id,tenant:tenantId}' -o table
az account set --subscription 'ID_ABONNEMENT_AUTORISE'
az provider show --namespace Microsoft.Compute --query registrationState -o tsv
az provider show --namespace Microsoft.Network --query registrationState -o tsv
```

Si un fournisseur n'est pas enregistré, demander à l'administrateur de l'activer avant la séance.
Ne pas changer d'abonnement au hasard. Identifier votre **IPv4 publique de sortie** par un service
approuvé par l'organisation ; sur VPN, il s'agit de la sortie VPN. La connaître ne nécessite pas de
la stocker dans Git. Préparer un identifiant personnel court (`alice01`) pour les noms et tags.

## Accès GitHub et vérification de départ

Forker le dépôt élève public `pilotcrew-io/azdevops-03-ansible-eleve` dans votre compte personnel
quand il est publié, puis cloner votre fork. Ne pas forker le corrigé privé dans un dépôt public.
Avant publication, utiliser la copie locale fournie par le formateur.
Préparer une clé **spécifique à l'atelier**, protégée par une phrase secrète :

```bash
ssh-keygen -t ed25519 -a 64 -f ~/.ssh/azd03_ed25519 -C 'atelier-azd03'
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/azd03_ed25519
```

La clé reste hors du dépôt. Ne jamais désactiver globalement StrictHostKeyChecking. Prévoir curl et
un terminal SSH ; réserver un emplacement privé aux inventaires et aux preuves contenant des IP.

## Si Azure est indisponible

Le parcours local compile le Java, teste les routes et analyse le playbook. Sur une vraie VM Linux
locale jetable avec systemd, les mêmes principes peuvent être exercés. Un simple conteneur sans
systemd ne valide pas le service. La note des critères Azure reste en attente ; ne pas inventer de
capture et ne pas déclarer la VM Azure créée.

## Empreintes sur macOS

Les agents Ubuntu et les commandes Linux utilisent `sha256sum`. Sur macOS, installer
GNU coreutils via le gestionnaire approuvé ou remplacer `sha256sum fichier` par
`shasum -a 256 fichier`, et `sha256sum --check SHA256SUMS` par
`shasum -a 256 -c SHA256SUMS`. Le format de manifeste SHA256 est compatible.
