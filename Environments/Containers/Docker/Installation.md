# Installation + Configuration

This document will be focused on Linux but I have written some additional notes for [Windows](Windows.md).

## Installation

### Installing Docker

```
sudo yum install docker
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker $USER
```

The setup of Docker is described in [Post-installation steps for Linux](https://docs.docker.com/install/linux/linux-postinstall/).

### Installing Docker Compose

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

The installation of Docker Compose is described in the [Docker Docs](https://docs.docker.com/compose/install/).



## Configuration

The Docker engine can be configured by tweaking `/etc/docker/daemon.json`.

This is can also be done through in Settings -> GUI (Advanced) on Docker for Windows

### BuildKit

Enable BuildKit as follows:

```json
{
    "features": {"buildkit": true}
}
```



### Logging

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

#### Further Reading

[Local File logging driver](https://docs.docker.com/config/containers/logging/local/)

[Docker Reference Architecture: Docker Logging Design and Best Practices](https://success.docker.com/article/logging-best-practices)



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



### Build Cache

You can specify the garbage collection policy in daemon.json:

```json
{
    "builder": {
        "gc": {
            "enabled": true,
            "policy": [
                {"keepStorage": "512MB", "filter": ["unused-for=168h"]},
                {"keepStorage": "30GB", "all": true}
            ]
        }
    }
}
```



## Orchestration

### Auto-Start Containers

Containers can be started automatically by setting their restart policy.

This is explained in the Docker documentation - [Start containers automatically](https://docs.docker.com/config/containers/start-containers-automatically/).

