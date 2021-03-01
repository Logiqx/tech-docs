# Tips and Tricks

## Presentations

### DockerCon18 Europe

Brandon Mitchell - [Tips and Tricks of the Docker Captains](https://www.youtube.com/watch?v=fdB31LScQzY&list)

The slides are available on [GitHub.](https://github.com/sudo-bmitch/presentations/tree/master/dc2018eu)

### DockerCon19 San Francisco

Brandon Mitchell - [Tips and Tricks of the Docker Captains](https://www.youtube.com/watch?v=woBI466WMR8)

The slides are available on [GitHub](https://github.com/sudo-bmitch/presentations/tree/master/dc2019).



## Tips

### Logging

The Docker daemon can be configured by tweaking `/etc/docker/daemon.json`.

This is can also be done through in Settings -> GUI (Advanced) on Docker for Windows

#### File Sizes

Limit the size of log files for new containers as follows:

```json
{
    "log-opts": {"max-size": "10m", "max-file": "3"}
}
```

#### Log Driver

The local logging driver was introduced in Docker 18.09, released 2018-11-08.

Switch to the optimized driver for new containers as follows:

```json
{
    "log-driver": "local"
}
```





### Networking

#### Subnet Collisions

Avoid subnet collisions on other networks by using BIP and specifying default address pools:

```json
{
    "bip": "10.15.0.1/24",
    "default-address-pools": [
        {"base": "10.20.0.0/16", "size": 24},
        {"base": "10.40.0.0/16", "size": 24}
    ]
}
```

#### Network debugging

Spin up another container containing the network debugging tools, connected to the network of the original container.



### Image Layers

#### Understanding layers

Identify what has been added / changed / deleted in each layer:

````sh
docker image build --no-cache --rm=false .
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



### Build Cache

You can specify the garbage collection policy in daemon.json:

```json
{
    "builder": {
        "gc": {
            "enabled": true,
            "policy": [
                {"keepStorage": "512MB", "filter": {"unused-for": {"168h": true}}},
                {"keepStorage": "2GB", "all": true}
            ]
        }
    }
}
```



### Mounts

#### BuildKit

This can be particularly useful as files can be used in the build but excluded from the final image:

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

Check the slide decks on GitHub!


