# Travailler dans votre fork

## 1. Créer sa copie personnelle

Une fois ce dépôt publié, cliquer sur **Fork** en haut de sa page GitHub, choisir votre compte et
conserver la branche principale. Le bouton **Use this template** crée un autre type de copie :
l'exercice demandé ici est bien un fork, afin de conserver le lien avec le dépôt pédagogique.

Ouvrir la page du fork et copier son URL depuis **Code**. Dans un terminal de votre poste :

```bash
git clone https://github.com/VOTRE_COMPTE/azdevops-03-ansible-eleve.git
cd azdevops-03-ansible-eleve
git remote -v
git switch -c atelier/realisation
```

Remplacer VOTRE_COMPTE par votre identifiant. `origin` doit viser **votre** compte. Si vous partez
d'une archive en attendant la publication, travailler localement et conserver vos fichiers ; le
formateur vous indiquera ensuite comment les reporter dans votre fork. Ne pas inventer un lien de PR.

## 2. Construire et expliquer

Lire `docs/PREREQUIS.md`, `docs/ATELIER.md`, puis les mémos. Ce dépôt contient volontairement les
consignes et des espaces de travail à compléter. Créer les fichiers d'application ou d'automatisation
demandés dans le tutoriel. Les commandes de vérification ne remplacent pas ces fichiers à écrire.

Conserver des commits courts qui décrivent les étapes. Avant chaque commit, lire `git diff` et
`git status --short`. Aucun mot de passe, jeton, clé privée, état ou plan Terraform, inventaire réel,
ni donnée personnelle ne doit entrer dans Git. Les captures et logs de preuve doivent être expurgés.

## 3. Ouvrir la bonne pull request

Après un commit et un push de votre branche, créer la PR avec :

- **base repository : VOTRE_COMPTE/azdevops-03-ansible-eleve** ;
- **base : main** ;
- **head repository : VOTRE_COMPTE/azdevops-03-ansible-eleve** ;
- **compare : atelier/realisation**.

GitHub propose parfois le dépôt d'origine comme destination : vérifier ces quatre champs pour que
la PR reste **dans votre fork**. Partager son URL avec le formateur par le canal habituel de la classe.

Dans l'atelier CI, ouvrir **Actions** dans votre fork et activer les workflows lorsqu'ils ont été
écrits et relus. Les secrets, environnements, règles de branches et connexions de service du formateur
ne sont pas copiés dans votre fork. Ne jamais coller un secret pour « débloquer » un run.

## 4. Livrer des preuves

Remplir le modèle de PR et le journal prévu par l'atelier : commandes exécutées, résultats observés,
différence avec les résultats attendus, liens du run et du commit, décision de diagnostic. Une preuve
non obtenue reste indiquée comme telle. Le nettoyage des ressources fait partie du rendu.

Un pair ou le formateur relit le travail et pose une question de compréhension. Expliquer en anglais
pendant une minute le résultat et une limitation. Le corrigé, conservé dans un dépôt privé séparé,
est une ressource de correction après votre restitution.

Référence : [documentation officielle sur les forks](https://docs.github.com/en/get-started/quickstart/fork-a-repo).
