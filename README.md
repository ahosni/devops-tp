# TP : Développement des applications modernes
## Prérequis
- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- [Git](https://git-scm.com/downloads)
## E01: Création de l'environnement de développement
1. Cloner spring-petclinic
```shell
git clone https://github.com/spring-projects/spring-petclinic.git
```
## E02: Lancement de l'application 
### DEV
1. Lancer l'application avec une base en memoire
   <details>
      <summary>Correction</summary>

      ```shell
      ./mvnw clean package -DskipTests=true
      java -jar ./target/*.jar 
      ```
   </details>
2. Lancer l'application avec le profil spring mysql `spring.profiles.active=mysql`, vous avez remarqué quoi ? 
   <details>
   <summary>Correction</summary>

      ```shell
      java -jar ./target/*.jar --spring.profiles.active=mysql
      ```
   </details>
3. Créer un conteneur avec une base de données mysql 
   - Faire une rechercher sur [Docker Hub](https://hub.docker.com/search?q=mysql) pour trouver la bonne image
```shell
 docker run -d --name mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -e MYSQL_DATABASE=petclinic  -e MYSQL_USER=petclinic -e MYSQL_PASSWORD=petclinic  -p 3306:3306 ?
```
4. Relancer l'application avec le profil mysql.
   <details>
      <summary>Correction</summary>

      ```shell
      java -jar ./target/*.jar --spring.profiles.active=mysql --spring.datasource.url=jdbc:mysql://localhost:3306/petclinic
      ```
   </details>

## E04: Simulation d'un deuxième environnement
2. Lancer une autre instance de la base de données qui ecoute sur le port `3307`
   <details>
   <summary>Correction</summary>

      ```shell
      docker run -d --name mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -e MYSQL_DATABASE=petclinic  -e MYSQL_USER=petclinic -e MYSQL_PASSWORD=petclinic  -p 3307:3306 mysql
      ```
   </details>

3. Lancer une autre instance de l'application sur le port `8081`

   <details>
   <summary>Correction</summary>

      ```shell
      java -jar ./target/*.jar --spring.profiles.active=mysql --spring.datasource.url=jdbc:mysql://localhost:3307/petclinic --server.port=8081
      ```
   </details>

## E05: Conteneuriser l'application

1. Ajouter un Dockerfile et créer une image contenenant l'application java (voir cet [exemple](https://www.docker.com/blog/9-tips-for-containerizing-your-spring-boot-code/) pour plus de details).

```dockerfile
FROM eclipse-temurin:17-jre-jammy
ARG JAR_FILE=target/*.jar
COPY ${JAR_FILE} app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

```shell
docker build -t spring/petclinic:3.7.0 .
```
2. Essayer de builder le jar lors de la création de l'image.
   <details>
      <summary>Correction</summary>

      ```Dockerfile
      FROM eclipse-temurin:17-jdk-jammy as builder
      WORKDIR /opt/app
      COPY .mvn/ .mvn
      COPY mvnw pom.xml ./
      RUN ./mvnw dependency:go-offline
      COPY ./src ./src
      RUN ./mvnw clean install -DskipTests


      FROM eclipse-temurin:17-jre-jammy
      WORKDIR /opt/app
      EXPOSE 8080
      COPY --from=builder /opt/app/target/*.jar /opt/app/*.jar
      ENTRYPOINT ["java", "-jar", "/opt/app/*.jar" ]
      
      ```
   </details>
3. Lancer un conteneur à partir de l'image créée (utiliser l'option `--rm`)
   <details>
      <summary>Correction</summary>

      ```shell
      docker run --rm -d --name petclinic -p 8080:8080 spring/petclinic:3.7.0
      ```
   </details>

4. Lancer un conteneur en passant l'argument `--spring.profiles.active=mysql` 
   <details>
      <summary>Correction</summary>

      ```shell
      docker run --rm --name petclinic -p 8080:8080 spring/petclinic:3.7.0 --spring.profiles.active=mysql
      ```
   </details>
4. Lancer un conteneur en passant la variabe d'environnement `spring.profiles.active=mysql` 
   <details>
      <summary>Correction</summary>

      ```shell
      docker run --rm  --name petclinic -p 8080:8080 -e SPRING_PROFILES_ACTIVE=mysql spring/petclinic:3.7.0
      ```
   </details>

6. Créer un conteneur pour l'application et un conteneur pour la base de données et les rattacher à un réseau de type bridge. 

   <details>
   <summary>Correction</summary>

      ```shell
      docker network create network1

      docker run -d --network network1 --name mysql1 -e MYSQL_DATABASE=petclinic -e MYSQL_USER=petclinic -e MYSQL_ROOT_PASSWORD=petclinic -e MYSQL_PASSWORD=petclinic mysql

      docker run -d --name petclinic1 --network network1 -p 8080:8080 spring/petclinic:3.7.0 --spring.profiles.active=mysql --spring.datasource.url=jdbc:mysql://mysql1/petclinic
      ```
   </details>

## E06: Utiliser Docker compose
1. Utiliser docker compose pour créer deux environnements dev et qualif

Au niveau de chaque environnement on doit avoir deux conteneurs, un conteneur pour la base de données et un conteneur pour l'application. 

<details>
<summary>Correction</summary>

```yaml
version: "3.8"

services:
  mysql1:
    image: mysql
    networks:
      - net1
    environment:
      - MYSQL_ROOT_PASSWORD=root
      - MYSQL_USER=petclinic
      - MYSQL_PASSWORD=petclinic
      - MYSQL_DATABASE=petclinic
    healthcheck:
      test: [ "CMD", "mysqladmin" ,"ping", "-h", "localhost" ]

  petclinic1:
    depends_on:
      mysql1:
        condition: service_healthy
    image: spring/petclinic:3.7.0
    networks:
      - net1
    ports:
      - "8080:8080"
    environment:
      - SPRING_PROFILES_ACTIVE=mysql
      - SPRING_DATASOURCE_URL=jdbc:mysql://mysql1/petclinic

  mysql2:
    image: mysql
    networks:
      - net2
    environment:
      - MYSQL_ROOT_PASSWORD=root
      - MYSQL_USER=petclinic
      - MYSQL_PASSWORD=petclinic
      - MYSQL_DATABASE=petclinic
    healthcheck:
      test: [ "CMD", "mysqladmin" ,"ping", "-h", "localhost" ]

  petclinic2:
    depends_on:
      mysql2:
        condition: service_healthy
    image: spring/petclinic:3.7.0
    networks:
      - net2
    ports:
      - "8081:8080"
    environment:
      - SPRING_PROFILES_ACTIVE=mysql
      - SPRING_DATASOURCE_URL=jdbc:mysql://mysql2/petclinic


networks:
  net1: {}
  net2: {}

```
</details>
<br/>

2. Créer un environnement de prod avec deux instances de l'application et une instance de la base de donnés.

3. [Homework] Sécuriser l'accès à l'environnement de prod en ajoutant un reverse-proxy [nginx](https://www.nginx.com), le seul port qui doit être exposé à l'exterieur est le port 443(https) du reverse-proxy, les connexions vers le port 80 doivent être redirigées vers le port 443.

> [Correction](homework/homework.yml)

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


# TP : [Gitlab CI/CD](GITLAB_TP.md)






