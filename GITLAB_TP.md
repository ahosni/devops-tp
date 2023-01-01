# TP : Gitlab CI/CD
## E01: Installation du runner Gitlab
- Installer  un runner gitlab sous docker en  utilisant le fichier [docker compose](gitlab/runner.yaml)

```shel
docker stack deploy --compose-file ./gitlab/runner.yaml runner  
```
## E02: Enregistrement du runner
Exécuter la commande suivante depuis le container du runner

```shell
 gitlab-runner register \
  --non-interactive \
  --url "https://gitlab.com/" \
  --registration-token "put you token here" \
  --executor "docker" \
  --docker-image alpine:latest \
  --description "docker-runner" \
  --maintenance-note "Free-form maintainer notes about this runner" \
  --run-untagged="true" \
  --locked="false" \
  --privileged="true" \
  --access-level="not_protected"
```
Cette commande va générer le fichier de configuration config.toml

```toml
concurrent = 1
check_interval = 0
shutdown_timeout = 0
run_untagged=true

[session_server]
  session_timeout = 1800

[[runners]]
  name = "docker-runner"
  url = "https://gitlab.com/"
  id = 19576087
  token = ""
  token_obtained_at = 2022-12-05T17:22:43Z
  token_expires_at = 0001-01-01T00:00:00Z
  executor = "docker"
  [runners.custom_build_dir]
  [runners.cache]
    MaxUploadedArchiveSize = 0
    [runners.cache.s3]
    [runners.cache.gcs]
    [runners.cache.azure]
  [runners.docker]
    tls_verify = false
    image = "alpine:latest"
    privileged = true
    disable_entrypoint_overwrite = false
    oom_kill_disable = false
    disable_cache = false
    volumes = ["/var/run/docker.sock:/var/run/docker.sock", "/cache"]
    shm_size = 0
```

Afin de pouvoir builder des images docker avec ce runner il faut autoriser le runner à accèder au socket docker

```
volumes = ["/var/run/docker.sock:/var/run/docker.sock", "/cache"]
```

## E03: Création d'un projet
- Créer un nouveau projet sous gitlab
- Configurer et pousser le projet petclinic sur gitlab

## E04: Gitlab CI
- Ajouter un job qui permet de compiler le projet sans lancer les tests.
- Ajouter un job qui permet de lancer les tests unitaires. 
- Ajouter la configuration qui permet d'afficher le resultat des tests dans l'interface de Gitlab
- Utiliser un cache pour ne pas retélécharger les dépendances à chaque lancement des jobs. 

## E05: Gitlab CD
- Ajouter un job qui s'execute en mode manuel uniquement sur un tag et qui simule le déploiement de l'application.

<details>
      <summary>Correction</summary>
     
      https://gitlab.com/ahosni/demo

   </details>