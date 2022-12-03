# TP : Gitlab CI/CD
## E01: Installation de Gitlab
- Installer Gitlab sous docker en  utilisant le fichier [docker compose](./gitlab/gitlab.yaml)

```shel
docker stack deploy --compose-file gitlab.yaml gitlab  
```


## E02: Création d'un projet
- Créer un nouveau projet sous gitlab
- Configurer et pousser le projet petclinic sur gitlab

## E03: Gitlab CI
- Ajouter un job qui permet de compiler le projet sans lancer les tests.
- Ajouter un job qui permet de lancer les tests unitaires. 
- Ajouter la configuration qui permet d'afficher le resultat des tests dans l'interface de Gitlab
- Utiliser un cache pour ne pas retélécharger les dépendances à chaque lancement des jobs.
- 

## E04: Gitlab CD
- Ajouter un job qui s'execute en mode manuel uniquement sur un tag et qui simule le déploiement de l'application.

