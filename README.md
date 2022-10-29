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

## E04: Simulation d'un deuxième environnement
2. Lancer une autre instance de la base de données qui ecoute sur le port `3307`
3. Lancer une autre instance de l'application sur le port `8081`

## E05: Conteneuriser l'application

1. Ajouter un Dockerfile et créer une image contenenant l'application java (voir cet [exemple](https://www.docker.com/blog/9-tips-for-containerizing-your-spring-boot-code/) pour plus de details).

```dockerfile
FROM eclipse-temurin:17-jre-jammy
ARG JAR_FILE=target/*.jar
COPY ${JAR_FILE} app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "/app.jar"]
```
2. Essayer d'optimiser la création de l'image.
3. Lancer un conteneur à partir de l'image créée (utiliser l'option `--rm`)
4. Lancer un conteneur en passant l'argument `--spring.profiles.active=mysql` 
4. Lancer un conteneur en passant la variabe d'environnement `spring.profiles.active=mysql` 
6. Créer un conteneur pour l'application et un conteneur pour la base de données et les rattacher à un réseau de type bridge. 


## E06: Utiliser Docker compose
1. Utiliser docker compose pour créer deux environnements dev et qualif

Au niveau de chaque environnement on doit avoir deux conteneurs, un conteneur pour la base de données et un conteneur pour l'application. 


2. Créer un environnement de prod avec deux instances de l'application et une instance de la base de donnés.

3. [Homework] Sécuriser l'accès à l'environnement de prod en ajoutant un reverse-proxy [nginx](https://www.nginx.com), le seul port qui doit être exposé à l'exterieur est le port 443(https) du reverse-proxy, les connexions vers le port 80 doivent être redirigées vers le port 443.

## E07: Vérifier les logs de l'application
Vérifier les logs au niveau du conteneur docker


## E08: Vérifier le gracefull shutdown
1. Lancer l'application java en local puis essayer de l'arrêter avec la commande `kill`
2.  Ajouter un ShutdownHook au niveau de l'application java et la relancer
```java
Runtime.getRuntime().addShutdownHook(new Thread(() -> {
			System.out.println("########## ShutdownHook Started ...  ###########");
}));
```
3. Essayer de l'arrêter, toujours avec la commande `kill`
4. Ajouter une instruction `sleep` de 20s  et refaire le test.
4. Refaire le test avec la commande `kill -9`
5. Lancer l'application dans un conteneur puis arrêter le conteneur avec la commande `docker stop`
6. Refaire le test en changeant le temps d'attente avec l'option `--time`





