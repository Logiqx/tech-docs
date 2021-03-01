# Useful Commands

# Docker

### System

#### System Information

Show the Docker version information:

```sh
docker version
```

Display system-wide information:

```sh
docker info
docker system info
```

Show docker disk usage:

```sh
docker system df
docker system df -v
```



### Containers

#### Container Information

List all containers:

```sh
docker ps -a
docker container ls -a
```

Display detailed information on one or more containers:

```
docker inspect
docker container inspect
```

List port mappings or a specific mapping for the container:

```sh
docker port
docker container port
```



#### Container Monitoring

List running containers:

```sh
docker ps
docker container ls
```

Fetch the logs of a container:

```
docker logs
docker container logs
```

Display the running processes of a container:

```sh
docker top
docker container top
```

Display a live stream of container(s) resource usage statistics:

```sh
docker stats
docker container stats
```



#### Container Operations

Run a command in a running container:

```sh
docker exec -it
docker container exec
```

Copy files/folders between a container and the local filesystem:

```sh
docker cp
docker container cp
```

Inspect changes to files or directories on a container's filesystem

```sh
docker diff
docker container diff
```

Export a container's filesystem as a tar archive

```sh
docker export
docker container export
```



### Images

#### Image Information

List images:

```sh
docker images
docker image ls
```

Display detailed information on one or more images:

```
docker inspect
docker image inspect
```

Show the history of an image:

```sh
docker history
docker image history
```



#### Image Operations

Build an image from a Dockerfile:

```sh
docker build
docker image build
```

Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE:

```sh
docker tag
docker image tag
```

Push an image or a repository to a registry:

```sh
docker push
docker image push
```

Pull an image or a repository from a registry:

```sh
docker pull
docker image pull
```

Save one or more images to a tar archive:

```sh
docker save
docker image save
```

Load an image from a tar archive:

```sh
docker load
docker image load
```



### Volumes

#### Volume Information

List volumes:

```sh
docker volume ls
```

Display detailed information on one or more volumes:

```
docker inspect
docker volume inspect
```



### Networks

#### Network Information

List networks:

```sh
docker network ls
```

Display detailed information on one or more networks:

```
docker inspect
docker network inspect
```



## Docker Compose

TODO


