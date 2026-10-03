# Sécurité de l'atelier

Ce dépôt est un support pédagogique. Ne poussez jamais une clé privée SSH, un mot de passe,
un jeton GitHub/Azure DevOps, un profil de publication ou un fichier d'identifiants Azure.
Les journaux et captures ne doivent contenir ni secret ni données personnelles ; masquer les
identifiants de tenant/abonnement avant une diffusion publique. Un identifiant n'est pas un secret,
mais il n'est pas nécessaire dans un rendu public.

Utiliser un groupe de ressources dédié et tagué avec le propriétaire de l'exercice. Les permissions
de déploiement sont limitées à ce groupe : aucun compte technique Contributor à l'échelle de
l'abonnement. La fédération d'identité dispense de stocker un secret de service principal.
Un administrateur habilité peut préparer les identités et les affectations de rôle.

Toute suppression doit vérifier le nom exact du groupe et ses tags `owner`/`workshop`, puis demander
une confirmation du nom. Ne jamais parcourir et supprimer des groupes de l'abonnement.
Les ressources sont facturables, selon région, offre, durée et consommation. Vérifier le budget avant
création ; supprimer à la fin et contrôler que la suppression est terminée.

Pour signaler une vulnérabilité, utiliser GitHub Private vulnerability reporting du dépôt s'il est
activé ; sinon contacter le formateur par le canal privé fourni au début du cours. Ne pas créer
une issue publique contenant un secret. Révoquer immédiatement tout secret exposé.
