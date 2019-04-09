# Get Started

## Get Started with Docker for Windows

Check Version:

```sh
PS C:\Users\Mike\Downloads\_misc_> docker --version
Docker version 18.03.1-ce, build 9ee9f40

PS C:\Users\Mike\Downloads\_misc_> docker version
Client:
 Version:      18.03.1-ce
 API version:  1.37
 Go version:   go1.9.5
 Git commit:   9ee9f40
 Built:        Thu Apr 26 07:12:48 2018
 OS/Arch:      windows/amd64
 Experimental: false
 Orchestrator: swarm

Server:
 Engine:
  Version:      18.03.1-ce
  API version:  1.37 (minimum version 1.12)
  Go version:   go1.9.5
  Git commit:   9ee9f40
  Built:        Thu Apr 26 07:22:38 2018
  OS/Arch:      linux/amd64
  Experimental: true
```

List Images:
```sh
PS C:\Users\Mike\Downloads\_misc_> docker image ls -a
REPOSITORY                 TAG          IMAGE ID            CREATED             SIZE
jupyter/minimal-notebook   latest       e3607c7b8f54        9 months ago        2.63GB
mysql                      latest       a8a59477268d        10 months ago       445MB
mysql/mysql-server         latest       02d081b9c73e        10 months ago       300MB
nginx                      latest       73acd1f0cfad        12 months ago       109MB
ubuntu                     latest       f975c5035748        12 months ago       112MB
hello-world                latest       f2a91732366c        15 months ago       1.85kB
```

List Containers:
```sh
PS C:\Users\Mike\Downloads\_misc_> docker container ls -a
CONTAINER ID    IMAGE                      COMMAND                  CREATED             cc40688b4667    jupyter/minimal-notebook   "tini -- start-noteb..."   9 months ago        
006d6be21ec7    mysql/mysql-server         "/entrypoint.sh mysq..."   9 months ago

Note: STATUS, PORTS, NAMES not shown above
```

Run bash within Ubuntu:
```sh
PS C:\Users\Mike\Downloads\_misc_> docker run --interactive --tty ubuntu bash
root@14631ea01a6d:/# hostname
14631ea01a6d
root@14631ea01a6d:/# exit
exit

PS C:\Users\Mike\Downloads\_misc_> docker run -it ubuntu bash
root@10766de32bbf:/# exit
exit

PS C:\Users\Mike\Downloads\_misc_> docker container rm amazing_brattain musing_boyd
amazing_brattain
```

Run webserver within nginx:

```sh
PS C:\Users\Mike\Downloads\_misc_> docker run --detach --publish 80:80 --name webserver nginx
bc8407b88937ffe4ece4dc74ef63f5b815de91a65c5e72cc07cb1d70a5b2a1d4

PS C:\Users\Mike\Downloads\_misc_> docker container ls
CONTAINER ID        IMAGE               COMMAND                  CREATED             
bc8407b88937        nginx               "nginx -g 'daemon of..."   33 seconds ago

PS C:\Users\Mike\Downloads\_misc_> docker container stop webserver
webserver

PS C:\Users\Mike\Downloads\_misc_> docker container rm webserver
webserver
```

Check environment in Alpine:

```sh
PS C:\Users\Mike\Downloads\_misc_> docker run alpine env
Unable to find image 'alpine:latest' locally
latest: Pulling from library/alpine
8e402f1a9c57: Pull complete
Digest: sha256:644fcb1a676b5165371437feaa922943aaf7afcfa8bfee4472f6860aad1ef2a0
Status: Downloaded newer image for alpine:latest

PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=1e24db4b9dec
HOME=/root
```

### Reference

Docker Docs - [Get started with Docker for Windows](https://docs.docker.com/docker-for-windows/)



## Get Started with Docker

### Part 1 - Orientation

Recap and Cheat Sheet:

```sh
## List Docker CLI commands
docker
docker container --help

## Display Docker version and info
docker --version
docker version
docker info

## Execute Docker image
docker run hello-world

## List Docker images
docker image ls

## List Docker containers (running, all, all in quiet mode)
docker container ls
docker container ls --all
docker container ls -aq
```



### Part 2 - Containers

Building a Container:

```sh
PS C:\Users\Mike\Downloads\_misc_\Docker> docker build --tag=friendlyhello .

PS C:\Users\Mike\Downloads\_misc_\Docker> docker run -p 4000:80 friendlyhello
 * Serving Flask app "app" (lazy loading)
 * Environment: production
   WARNING: Do not use the development server in a production environment.
   Use a production WSGI server instead.
 * Debug mode: off
 * Running on http://0.0.0.0:80/ (Press CTRL+C to quit)

PS C:\Users\Mike\Downloads\_misc_\Docker> docker run -d -p 4000:80 friendlyhello
7573cc900e600e2e045c2d163f072deaab761c2191a20d5a41088870309f45d4
```

Publishing an Image:

```sh
PS C:\Users\Mike\Downloads\_misc_\Docker> docker login
Authenticating with existing credentials...
Login Succeeded

PS C:\Users\Mike\Downloads\_misc_\Docker> docker tag friendlyhello logiqx/get-started:part2

PS C:\Users\Mike\Downloads\_misc_\Docker> docker images friendlyhello
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
friendlyhello       latest              c90d819f95f7        9 minutes ago       131MB

PS C:\Users\Mike\Downloads\_misc_\Docker> docker image ls
REPOSITORY                 TAG             IMAGE ID            CREATED             SIZE
logiqx/get-started         part2           c90d819f95f7        9 minutes ago       131MB
friendlyhello              latest          c90d819f95f7        9 minutes ago       131MB
alpine                     latest          5cb3aa00f899        39 hours ago        5.53MB
python                     2.7-slim        8559620b5b0d        4 days ago          120MB
jupyter/minimal-notebook   latest          e3607c7b8f54        9 months ago        2.63GB
mysql                      latest          a8a59477268d        10 months ago       445MB
mysql/mysql-server         latest          02d081b9c73e        10 months ago       300MB
nginx                      latest          73acd1f0cfad        12 months ago       109MB
ubuntu                     latest          f975c5035748        12 months ago       112MB
hello-world                latest          f2a91732366c        15 months ago       1.85kB

PS C:\Users\Mike\Downloads\_misc_\Docker> docker push logiqx/get-started:part2
The push refers to repository [docker.io/logiqx/get-started]
e9d4b4bde37e: Pushed
8a913e4042cc: Pushed
09832a837693: Pushed
c39d784764e0: Mounted from library/python
4bad4dc0f0f2: Mounted from library/python
d443bc70f9c5: Mounted from library/python
6744ca1b1190: Mounted from library/python
part2: digest: sha256:76f24c9defaad680c8c605e3f9043b500d9c272d7af764a03bac3c122e1082d3 size: 1787
```

Recap and Cheat Sheet:

```sh
docker build -t friendlyhello .  # Create image using this directory's Dockerfile
docker run -p 4000:80 friendlyhello  # Run "friendlyname" mapping port 4000 to 80
docker run -d -p 4000:80 friendlyhello         # Same thing, but in detached mode
docker container ls                                # List all running containers
docker container ls -a             # List all containers, even those not running
docker container stop <hash>           # Gracefully stop the specified container
docker container kill <hash>         # Force shutdown of the specified container
docker container rm <hash>        # Remove specified container from this machine
docker container rm $(docker container ls -a -q)         # Remove all containers
docker image ls -a                             # List all images on this machine
docker image rm <image id>            # Remove specified image from this machine
docker image rm $(docker image ls -a -q)   # Remove all images from this machine
docker login             # Log in this CLI session using your Docker credentials
docker tag <image> username/repository:tag  # Tag <image> for upload to registry
docker push username/repository:tag            # Upload tagged image to registry
docker run username/repository:tag                   # Run image from a registry
```



### Part 3 - Services

Creating a Service:

```sh
PS C:\Users\Mike\Downloads\_misc_\Docker> docker swarm init
Swarm initialized: current node (xuzyee2jbm18ix6sr8ylzxpn4) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-1xkdg90irl690by69hm4sah4n3tcpo036nq4j70dhtepwkzd9x-4hq0uo78bwf4ir1yxenssf4g5 192.168.65.3:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.

PS C:\Users\Mike\Downloads\_misc_\Docker> docker stack deploy -c docker-compose.yml getstartedlab
Creating network getstartedlab_webnet
Creating service getstartedlab_web

PS C:\Users\Mike\Downloads\_misc_\Docker> docker service ls
ID                  NAME                MODE                REPLICAS            IMAGE                      PORTS
evua7gsrfscw        getstartedlab_web   replicated          5/5                 logiqx/get-started:part2   *:4000->80/tcp

PS C:\Users\Mike\Downloads\_misc_\Docker> docker stack services getstartedlab
ID                  NAME                MODE                REPLICAS            IMAGE                      PORTS
evua7gsrfscw        getstartedlab_web   replicated          5/5                 logiqx/get-started:part2   *:4000->80/tcp

PS C:\Users\Mike\Downloads\_misc_\Docker> docker service ps getstartedlab_web
ID                  NAME                  IMAGE                      NODE                    DESIRED STATE       CURRENT STATE           ERROR               PORTS
7mno7p9rq7f3        getstartedlab_web.1   logiqx/get-started:part2   linuxkit-00155db9b106   Running             Running 2 minutes ago
tntiiszdfvsx        getstartedlab_web.2   logiqx/get-started:part2   linuxkit-00155db9b106   Running             Running 2 minutes ago
ev6l5pmx238v        getstartedlab_web.3   logiqx/get-started:part2   linuxkit-00155db9b106   Running             Running 2 minutes ago
m8dl3h6odid6        getstartedlab_web.4   logiqx/get-started:part2   linuxkit-00155db9b106   Running             Running 2 minutes ago
vkau672wxgd3        getstartedlab_web.5   logiqx/get-started:part2   linuxkit-00155db9b106   Running             Running 2 minutes ago

PS C:\Users\Mike\Downloads\_misc_\Docker> docker stack ps getstartedlab
ID                  NAME                  IMAGE                      NODE                    DESIRED STATE       CURRENT STATE           ERROR               PORTS
7mno7p9rq7f3        getstartedlab_web.1   logiqx/get-started:part2   linuxkit-00155db9b106   Running             Running 6 minutes ago
tntiiszdfvsx        getstartedlab_web.2   logiqx/get-started:part2   linuxkit-00155db9b106   Running             Running 6 minutes ago
ev6l5pmx238v        getstartedlab_web.3   logiqx/get-started:part2   linuxkit-00155db9b106   Running             Running 6 minutes ago
m8dl3h6odid6        getstartedlab_web.4   logiqx/get-started:part2   linuxkit-00155db9b106   Running             Running 6 minutes ago
vkau672wxgd3        getstartedlab_web.5   logiqx/get-started:part2   linuxkit-00155db9b106   Running             Running 6 minutes agodock

PS C:\Users\Mike\Downloads\_misc_\Docker> docker stack deploy -c docker-compose.yml getstartedlab
Updating service getstartedlab_web (id: evua7gsrfscw0o6h79l3vaqft)

PS C:\Users\Mike\Downloads\_misc_\Docker> docker stack rm getstartedlab
Removing service getstartedlab_web
Removing network getstartedlab_webnet

PS C:\Users\Mike\Downloads\_misc_\Docker> docker swarm leave --force
Node left the swarm.
```

Recap and Cheat Sheet:

```sh
docker stack ls                                            # List stacks or apps
docker stack deploy -c <composefile> <appname>  # Run the specified Compose file
docker service ls                 # List running services associated with an app
docker service ps <service>                  # List tasks associated with an app
docker inspect <task or container>                   # Inspect task or container
docker container ls -q                                      # List container IDs
docker stack rm <appname>                             # Tear down an application
docker swarm leave --force      # Take down a single node swarm from the manager
```



### Part 4 - Swarms

TODO



#### References

Docker Docs - [Get Started with Docker](https://docs.docker.com/get-started/)



## Useful Commands

Checking Disk Usage:

```sh
PS C:\Users\Mike\Downloads\_misc_> docker system df -v
Images space usage:

REPOSITORY                 TAG                 IMAGE ID            CREATED ago         SIZE                SHARED SIZE         UNIQUE SiZE         CONTAINERS
alpine                     latest              5cb3aa00f899        38 hours ago ago    5.529MB             0B                  5.529MB             0
jupyter/minimal-notebook   latest              e3607c7b8f54        9 months ago ago    2.633GB             112.4MB             2.521GB             1
mysql                      latest              a8a59477268d        10 months ago ago   444.8MB             0B                  444.8MB             0
mysql/mysql-server         latest              02d081b9c73e        10 months ago ago   300.1MB             0B                  300.1MB             1
nginx                      latest              73acd1f0cfad        12 months ago ago   108.6MB             0B                  108.6MB             0
ubuntu                     latest              f975c5035748        12 months ago ago   112.4MB             112.4MB             0B                  0
hello-world                latest              f2a91732366c        15 months ago ago   1.848kB             0B                  1.848kB             0

Containers space usage:

CONTAINER ID        IMAGE                      COMMAND                  LOCAL VOLUMES       SIZE                CREATED ago         STATUS                      NAMES
cc40688b4667        jupyter/minimal-notebook   "tini -- start-noteb..."   1                   58.8MB              9 months ago ago    Exited (255) 13 hours ago   notebook
006d6be21ec7        mysql/mysql-server         "/entrypoint.sh mysq..."   1                   2.47kB              9 months ago ago    Exited (0) 9 months ago     mysql

Local Volumes space usage:

VOLUME NAME                                                        LINKS               SIZE
e403a201a0c660360bdeb8287f9ce2bf7d90868a2adc12cb458a3c9d9851a016   1                   168.1MB
scratch                                                            1                   3.388MB

Build cache usage: 0B
```

Docker Information:

```sh
PS C:\Users\Mike\Downloads\_misc_> docker info
Containers: 2
 Running: 0
 Paused: 0
 Stopped: 2
Images: 7
Server Version: 18.03.1-ce
Storage Driver: overlay2
 Backing Filesystem: extfs
 Supports d_type: true
 Native Overlay Diff: true
Logging Driver: json-file
Cgroup Driver: cgroupfs
Plugins:
 Volume: local
 Network: bridge host ipvlan macvlan null overlay
 Log: awslogs fluentd gcplogs gelf journald json-file logentries splunk syslog
Swarm: inactive
Runtimes: runc
Default Runtime: runc
Init Binary: docker-init
containerd version: 773c489c9c1b21a6d78b5c538cd395416ec50f88
runc version: 4fc53a81fb7c994640722ac585fa9ca548971871
init version: 949e6fa
Security Options:
 seccomp
  Profile: default
Kernel Version: 4.9.87-linuxkit-aufs
Operating System: Docker for Windows
OSType: linux
Architecture: x86_64
CPUs: 2
Total Memory: 5.8GiB
Name: linuxkit-00155db9b104
ID: FRBA:PKPR:YTWE:XSOS:44XE:DOY6:5KOF:YPP7:GCQV:VHSF:PHUH:NBUR
Docker Root Dir: /var/lib/docker
Debug Mode (client): false
Debug Mode (server): true
 File Descriptors: 19
 Goroutines: 36
 System Time: 2019-03-09T12:38:55.6290705Z
 EventsListeners: 1
Registry: https://index.docker.io/v1/
Labels:
Experimental: true
Insecure Registries:
 127.0.0.0/8
Live Restore Enabled: false
```

