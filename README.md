# DevOps
Cours de DevOps

## 1-1 For which reason is it better to run the container with a flag -e to give the environment variables rather than put them directly in the Dockerfile?

It is better to pass variables via -e during execution because, for security reasons, this avoids exposing passwords in writing on the easily accessible dockerfile. Because everyone who has access to the image sees the codes

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
Multi-stage build allows to have a clean and optimized  Docker image, while automating the compilation of the application without polluting the final image with development tools.


`FROM eclipse-temurin:21-jdk-alpine AS myapp-build` -> Uses a full JDK image to compile Java (SpringBoot).       
`ENV MYAPP_HOME=/opt/myapp` -> Declares an environment variable to avoid repeating the path .       
`WORKDIR $MYAPP_HOME` -> Sets the working directory inside the image, all the next commands run here.                     
`RUN apk add --no-cache maven` -> Installs Maven using Alpine’s package manager (`apk`).                                            
`COPY pom.xml .`  -> Copies the `pom.xml` file into the container.       
`COPY src ./src` ->Copies the project source code.                                                                   
`RUN mvn package -DskipTests` -> Compiles the project and builds a `.jar` file in `target/`, skipping tests.         

## 1-5 Why do we need a reverse proxy?

A reverse proxy acts as a single point of entry for all incoming requests. It is important in the project for:

* Isolation and security: so that the backend (Spring Boot) and the database are not exposed to the public.

* Simplification of access: Users access localhost without having to know internal ports or paths.

* Conformity: It can serve both the static frontend (index.html) and forward calls to the API.

## 1-6 Why is docker-compose so important?

Docker Compose allows to define and manage multiple containers with a single command.

In this lab, it's important because it simplifies the management of dependencies between services (depends_on, networks, volumes) and allows you to launch the entire architecture (backend, database, httpd) with a single command (docker-compose up).

## 1-7 Document docker-compose most important commands.

docker-compose up -> Start all defined services.  
docker-compose up --build -> Rebuilds images before booting  
docker-compose down -> Stops and deletes containers, networks, temporary volumes  
docker-compose ps -> Lists containers managed by Compose  
docker-compose logs (-f) -> Displays logsfor debugging (in live with -f)  

## 1-8 Document your docker-compose file.

File commented directly on the code

## 1-9 Document your publication commands and published images in dockerhub

What I did :  
`docker tag mcatillon/postgres_custom mcatillon/postgres_custom:1.0`  
`docker tag springboot-api mcatillon/springboot-api:1.0`  
`docker tag my-http-server mcatillon/my-http-server:1.0`  
  
`docker push mcatillon/postgres_custom:1.0`  
`docker push emcatillon/springboot-api:1.0`  
`docker push mcatillon/my-http-server:1.0`  

## 1-10 Why do we put our images into an online repo?

Publishing images to a registry allows to share and collaborate with others who can pull the images. It also allows for automatic depoting with CI/CD servers. Finally, tags allow for version management of builts. Here we can pull the images in the next step of the labs.

## 2-1 What are testcontainers?

Testcontainers is a Java library that allows you to automatically launch Docker containers while running tests.

## 2-2 For what purpose do we need to use secured variables ?

Secure variables (GitHub secrets) are used to protect sensitive information such as Docker Hub credentials (DOCKERHUB_USERNAME, DOCKERHUB_TOKEN).  
Here, we want to automatically connect to Docker Hub in the GitHub Actions workflow without exposing the credentials in the code.

## 2-3 Why did we put needs: build-and-test-backend on this job? Maybe try without this and you will see!

This instruction means that the build-and-push-docker-image job will only run if the test-backend job succeeds. This prevents Docker images from being built or published if the tests fail.

## 2-4 For what purpose do we need to push docker images?

Pushing a Docker image to Docker Hub makes it available to other environments (production, staging, CI/CD, other developers, etc.).  
Here, pushing the image prepares for continuous delivery (CD).
This image can then be easily deployed to a server.  
Without this step, the image remains local.

## 3-1 Document your inventory and base commands

We can see the commentaries in `ansible/inventories/setup.yml`

## 3-2 Document your playbook

We can see the commentaries in `ansible/playbook.yml`

## 3-3 Document your docker_container tasks configuration.

We can see the commentaries in `ansible/roles/install_docker/tasks/main.yml`

## Is it really safe to deploy automatically every new image on the hub ? explain. What can I do to make it more secure?

No, it's not safe in production because an automatically pushed image may contain unstable, untested, or compromised code.  

An error in the image could break the service in production without human oversight.  

To make deployment more secure, you can deploy only tagged versions and not the latest images, or add a post-deployment testing step. You can also deploy first to a pre-production server before going live. Finally, you can do as in these labs and use manual validations via Github Actions.