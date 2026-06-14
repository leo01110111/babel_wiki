# Docker Command CheatSheet

[Category:Docker](Category%3ADocker.md)
[Category:Software Development](Category%3ASoftware%20Development.md)
[Category:Containerization](Category%3AContainerization.md)
[Category:DevOps](Category%3ADevOps.md)
[Category:System Administration](Category%3ASystem%20Administration.md)
[Category:Cloud Computing](Category%3ACloud%20Computing.md)
[Category:Tips](Category%3ATips.md)

[Docker](https://www.docker.com/) is an open-source platform designed to make it easier to create, deploy, and run applications by using containers. Containers allow developers to package up an application with all parts it requires, such as libraries and other dependencies, and ship it all out as one package. By doing so, the developer can be sure that the application will run on any other machine regardless of any customized settings that machine might have, which could differ from the machine used for writing and testing the code. This page provides a comprehensive cheat sheet for various Docker commands categorized into building, running, and modifying containers and images. It serves as a quick reference guide for developers and system administrators working with Docker.

## Build
### Building and Managing Images
- **Build an image:** 
`docker build -t <image-name>:<tag> <path>`
- **List images:** 
`docker images`
- **Remove an image:** 
`docker rmi <image-id>`
- **Save an image to a tar file:** 
`docker save -o <path-for-tar-file> <image-name>:<tag>`
- **Load an image from a tar file:** 
`docker load -i <path-to-tar-file>`

## Run
### Running and Managing Containers
- **Run a container:** 
`docker run <image-name>`
- **Run a container in detached mode:** 
`docker run -d <image-name>`
- **Run a container and publish a port:** 
`docker run -p <host-port>:<container-port> <image-name>`
- **Run a container and mount a volume:** 
`docker run -v <host-directory>:<container-path> <image-name>`
- **Run a container and set an environment variable:** 
`docker run -e "KEY=VALUE" <image-name>`
- **Run a container with a specific user (e.g., root):** 
`docker run -u 0 <image-name>`
- **Run a container with a specific user or UID/GID:** 
`docker run -u <user> <image-name>` 
`docker run -u <uid>:<gid> <image-name>`
- **Run a container and give it a name:** 
`docker run --name <container-name> <image-name>`
- **Run a container and link it to a network:** 
`docker run --network <network-name> <image-name>`
- **Run a container with limited memory:** 
`docker run --memory <memory-limit> <image-name>`
- **Run a container and restart it on-failure:** 
`docker run --restart on-failure <image-name>`
- **Run a container and override the default command:** 
`docker run <image-name> <command>`

## Modify
### Managing Containers and Networks
- **List running containers:** 
`docker ps`
- **List all containers (including stopped):** 
`docker ps -a`
- **Stop a running container:** 
`docker stop <container-id>`
- **Remove a container:** 
`docker rm <container-id>`
- **Execute a command inside an existing container:** 
`docker exec -it <container-id> <command>`
- **Create a network:** 
`docker network create <network-name>`
- **Connect a container to a network:** 
`docker network connect <network-name> <container-id>`
- **Disconnect a container from a network:** 
`docker network disconnect <network-name> <container-id>`
- **View logs for a container:** 
`docker logs <container-id>`
- **Copy files/folders between a container and the local filesystem:** 
`docker cp <container-id>:<path> <local-path>` 
`docker cp <local-path> <container-id>:<path>`

### Image Distribution
- **Push an image to Docker Hub:** 
`docker push <image-name>:<tag>`
- **Pull an image from a repository:** 
`docker pull <image-name>:<tag>`
