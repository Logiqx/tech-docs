# Tips and Tricks

## DockerCon18 Europe

*Docker Captain Brandon Mitchell will help you accelerate your adoption of Docker containers by delivering tips and tricks on getting the most out of Docker. Topics include managing disk usage, preventing subnet collisions, debugging container networking, understanding image layers, getting more value out of the default volume driver, and solving the UID/GID permission issues with volumes in a way that allows images to be portable from any developer laptop and to production.*

Video - [Tips and Tricks of the Docker Captains](https://www.youtube.com/watch?v=fdB31LScQzY&list=PLkA60AVN3hh_DVyQ13qGheO_Jg7jcAPcv&index=10)

The slides are available on [GitHub](https://github.com/sudo-bmitch/presentations/tree/master/dc2018eu)

### Logging

#### Log file sizes

Limit the size of log files from new containers by tweaking /etc/docker/dameon.json:

```json
{
    "log-opts": {"max-size": "10m", "max-file": "3"}
}
```

Note: This is done through in Settings -> GUI (Advanced) on Docker for Windows

### Networking

#### Subnet Collisions

Avoid subnet collisions on public networks by specifying the default address pools:

```json
{
    "default-address-pools"
}
```

#### Network debugging

Spin up another container containing the network debugging tools, connected to the network of the oroginal container.

### Image Layers

#### Understanding layers

Identify what has been added / changed / deleted in each layer:

````sh
DOCKER_BUILDKIT=0 docker build --no-cache -rm=false .
docker container diff [container]
````

A = added, C = changed ("copy on write"), D = deleted

#### Merge RUN commands

This is a well known best practice to reduce the number of layers and size of images:

```sh
RUN apt-get update \
 && apt-get install -y curl \
 && rm -rf /var/lib/apt/lists/*
```

#### Multi-Stage Builds

These are also a well known best practice to reduce the number of layers and size of images.

They are particularly useful for languages such as Java, Go, etc.

Note: The build layers are not shipped with the image but they are cached on the build server.

### Mounts

#### BuildKit

This can be particularly useful in dockerfiles as files can be used in the build but excluded from the image:

```
--mount=type=bind
--mount=type=cache
--mount=type=secret
```

#### NFS and EXT4 Mounts

It is possible to mount NFS and EXT4 volumes in Docker containers.

#### Overlay Filesystem

An overlay file system ensures that writes to volumes are only visible to the container.

### Development

#### Fixing UID / GID

Different UID / GID on the host can be problematic on developer machines.

Brandon has created a script to fix these issues when the container starts up.


