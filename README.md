# TP : Développement des applications modernes
## Prérequis
- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- [Git](https://git-scm.com/downloads)
## E01: Création de l'environnement de développement
1. Cloner spring-petclinic
```shell
git clone https://github.com/spring-projects/spring-petclinic.git
```
2. Préparer l’environnement de développement avec VS code et  l'extension dev-container
## E02: Lancement de l'application 
### DEV
1. Lancer l'application avec une base en memoire
2. Lancer l'application avec le profil spring mysql `spring.profiles.active=mysql`, vous avez remarqué quoi ? 
3. Créer un conteneur avec une base de données mysql 
   - Faire une rechercher sur [Docker Hub](https://hub.docker.com/search?q=mysql) pour trouver la bonne image
```shell
docker run -d --name mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -p 3306:3306  ?
```
4. Relancer l'application avec le profil mysql.

## E04: Simulation de la Prod
1. Lancer une autre instance de la base de données qui ecoute sur le port `3307`
2. Lancer une autre instance de l'application sur le port `8081`


## E05: Utiliser Docker compose
1. Utiliser docker compose pour créer les deux environnements dev et prod

## E05: Vérifier les logs de l'application
Vérifier les logs au niveau du conteneur docker


## E06: Vérifier le gracefull shutdown
