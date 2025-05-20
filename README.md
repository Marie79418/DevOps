# DevOps
Cours de DevOps

## 1-1 For which reason is it better to run the container with a flag -e to give the environment variables rather than put them directly in the Dockerfile?

It is better to pass variables via -e during execution because for security reasons, this avoids exposing passwords in writing on the easily accessible dockerfile. Because everyone who has access to the image sees the codes

## 1-2 Why do we need a volume to be attached to our postgres container?

If there is no volume, the data is stored in the container itself.
When the container is deleted, the data is lost.
A volume persists outside the container's lifecycle, so the data survives container reboots, shutdowns, and re-creations.

## 1-3 Document your database container essentials: commands and Dockerfile.

* how is the dockerfile  
    FROM postgres:17.2-alpine  
  
ENV POSTGRES_DB=db \  
   POSTGRES_USER=usr \  
   POSTGRES_PASSWORD=pwd  


* Create the image the image : docker build -t mcatillon/postgres_custom .
* Create the network : docker network create app-network

* Start the container: docker run -d --name postgres_db --network app-network mcatillon/postgres_custom
* This command starts a new Postgres container with the name my-postgres.

* Start the adminer : docker run -d -p 8091:8080 --network app-network --name adminer adminer

* create the volume to store the data : mkdir -p ./pgdata

* remove the container and not the volume : docker rm -f postgres_db

* relauch the container with the volume : docker run -d --name postgres_db --network app-network -v ${PWD}/pgdata:/var/lib/postgresql/data mcatillon/postgres_custom

## 1-4 Why do we need a multistage build? And explain each step of this dockerfile.

A multi-stage build in Docker allows to separate the build (compilation) and execution (runtime) stages by using multiple Docker images in a single Dockerfile.  
Multi-stage build allows you to have a clean, optimized, secure, and production-ready Docker image, while automating the compilation of the application without polluting the final image with development tools.

| Line                                      | Explanation                                                                                          |
|-------------------------------------------|----------------------------------------------------------------------------------------------------|
| `FROM eclipse-temurin:21-jdk-alpine AS myapp-build` | Uses a full JDK image to compile Java (Spring Boot).       |
| `ENV MYAPP_HOME=/opt/myapp`                | Declares an environment variable to avoid repeating the path later.                    |
| `WORKDIR $MYAPP_HOME`                       | Sets the working directory inside the image, all the next commands run here.                     |
| `RUN apk add --no-cache maven`              | Installs Maven using Alpine’s package manager (`apk`).                                             |
| `COPY pom.xml .`                            | Copies the `pom.xml` file into the container.        |
| `COPY src ./src`                            | Copies the project source code.                                                                    |
| `RUN mvn package -DskipTests`               | Compiles the project and builds a `.jar` file in `target/`, skipping tests.                        |

## 1-5 Why do we need a reverse proxy?

1-5 Why do we need a reverse proxy?
Reasons for using a reverse proxy like Apache or Nginx include:
Security, as a shield between clients and the backend server: requests can be filtered, blocked, and redirected.

Backend Hiding
Clients only interact with the reverse proxy. The backend can be hidden and protected, even on another network.

Load Balancing
A reverse proxy can distribute requests between multiple backend servers.

SSL Termination (HTTPS)
It manages SSL encryption to relieve backend applications.

URL Rewriting / Redirections
It can rewrite paths, redirecting to different microservices depending on the routes (/api, /admin, etc.).

## ou 

Un reverse proxy comme Apache ou Nginx agit comme point d’entrée unique pour toutes les requêtes entrantes. Il est essentiel dans ce contexte pour plusieurs raisons :

Isolation & sécurité : Le backend (Spring Boot) et la base de données ne sont pas exposés directement à Internet.

Simplification de l’accès : Les utilisateurs accèdent à localhost sans avoir à connaître les ports internes ou les chemins des services.

Extensibilité : Permet d’ajouter facilement de la mise en cache, des certificats SSL, ou un équilibrage de charge (load balancing).

Uniformisation : Il peut servir à la fois le front statique (index.html) et faire suivre les appels vers l’API.

## 1-6 Why is docker-compose so important?

Docker Compose allows you to define and manage multiple containers with a single command.

In this lab, it's important because it simplifies the management of dependencies between services (depends_on, networks, volumes) and allows you to launch the entire architecture (backend, database, httpd) with a single command (docker-compose up).

## 1-7 Document docker-compose most important commands.

test