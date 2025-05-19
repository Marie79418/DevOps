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
