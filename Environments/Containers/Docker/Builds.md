# Building Images

## General

### Multi-stage Builds

Using a [multi-stage build](https://docs.docker.com/develop/develop-images/multistage-build/) to convert a Notebook to a simple Python script is advantageous.

I've used this approach in a number of my projects on GitHub:

- [wca-db](https://github.com/Logiqx/wca-db/blob/master/Dockerfile) - World Cube Association - Scripts to download and optimise the database
- [wca-ipy](https://github.com/Logiqx/wca-ipy/blob/master/Dockerfile) - iPython notebooks to produce (un)official rankings for the senior cubing community

For example:

```dockerfile
# Base image versions
ARG NOTEBOOK_VERSION=c39518a3252f
ARG PYTHON_VERSION=3.8
ARG ALPINE_VERSION=3.10

# Jupyter notebook image is used as the builder
FROM jupyter/base-notebook:${NOTEBOOK_VERSION} AS builder

# Copy the required project files
WORKDIR /home/jovyan/work/wca-db
COPY --chown=jovyan:users python/*.*py* ./python/
COPY --chown=jovyan:users sql/*.sql ./sql/

# Convert Jupyter notebooks to regular Python scripts
RUN jupyter nbconvert --to python python/*.ipynb && \
    rm python/*.ipynb

# Ensure project file permissions are correct
RUN chmod 755 python/*.py && \
    chmod 644 sql/*.sql

# Create final image from Python 3 + Beautiful Soup 4 on Alpine Linux
FROM logiqx/python-bs4:${PYTHON_VERSION}-alpine${ALPINE_VERSION}

# Install MySQL client
RUN apk add --no-cache mysql-client=~10.3

# Note: Jovian is a fictional native inhabitant of the planet Jupiter
ARG PY_USER=jovyan
ARG PY_GROUP=jovyan
ARG PY_UID=1000
ARG PY_GID=1000

# Create the Python user and work directory
RUN addgroup -g ${PY_GID} -S ${PY_GROUP} && \
    adduser -u ${PY_UID} -S ${PY_USER} -G ${PY_USER} && \
    mkdir -p /home/${PY_USER}/work && \
    chown ${PY_USER} /home/${PY_USER}/work

# Environment variables used by the Python scripts
ENV MYSQL_HOSTNAME=mariadb
ENV MYSQL_DATABASE=wca
ENV MYSQL_USER=wca

# Copy project files from the builder
USER ${PY_USER}
WORKDIR /home/${PY_USER}/work
COPY --from=builder --chown=jovyan:jovyan /home/jovyan/work/ ./

# Define the command / entrypoint
CMD ["python3"]
```

#### Maximise Caching

Counter to the normal policy of chaining RUN commands try to split things up to maximise caching.



### BuildKit

BuildKit is faster than the traditional Docker build engine and has additional features.

It is also worth noting that docker.ignore is redundant due to the context being intelligent.

1. Set it as an environment variable with `export DOCKER_BUILDKIT=1`.

2. Start your `build` or `run` command with `DOCKER_BUILDKIT=1`.

3. Set the configuration in `/etc/docker/daemon.json` then restart Docker.

```
{
	"features": {"buildkit": true}
}
```

#### Clearing the Build Cache

```
docker build prune ...
```

You can also specify the garbage collection policy in `/etc/docker/daemon.json`:

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



### Tagging

The git revision can be useful for CI/CD builds since it is unique and can be useful as a tag.

Here are two simple commands to get the Git commit id:

```
git describe --always --abbrev=12
git rev-parse --short=12 HEAD
```

The above commands can then be used as an image tag during the Docker build:

```
docker build . -t petition:$(git rev-parse --short=12 HEAD)
docker tag petition:$(git rev-parse --short=12 HEAD) petition:latest
```



### Docker CMD vs ENTRYPOINT

"set -ex" unnecessary in Dockerfile

<https://www.ctl.io/developers/blog/post/dockerfile-entrypoint-vs-cmd/>



## My Images

This section lists some of the images that I have created using in Docker.

### Python+ Beautiful Soup

The base image for my Python deployments is on DockerHub:

- [python-bs4](https://hub.docker.com/repository/docker/logiqx/python-bs4) - Python 3 + Beautiful Soup 4 on Alpine Linux

The hardest bit of the python-bs4 build relates to [lxml](https://lxml.de/installation.html) but it is fully documented on [GitHub](https://github.com/Logiqx/python-bs4).

### Jupyter

I use Jupyter notebooks for ad hoc projects written in Python.

I have documented the way I have set up [Jupyter](../../../Development/Languages/Python/README.md) using Docker Compose.



### MySQL

I have used MySQL for WCA data analysis and as part of an AMP / EMP stack.

I have documented the way I have set up [MySQL](../../../Services/Databases/MySQL/README.md) using Docker Compose.



### MariaDB

I have used MariaDB for WCA data analysis and as part of an AMP / EMP stack.

I have documented the way I have set up [MariaDB](../../../Services/Databases/MariaDB/README.md) using Docker Compose.



### NGINX

TODO



### PHP-FPM

TODO



### WordPress

TODO - [Dockerizing Wordpress with Nginx and PHP-FPM](https://www.google.com/amp/s/www.howtoforge.com/tutorial/dockerizing-wordpress-with-nginx-and-php-fpm/amp/)



## Third Party Examples

### LAMP

Cloudreach - [Containerize This: PHP/Apache/MySQL](https://www.mysqlcloudreach.com/blog/containerize-this-php-apache-mysql-within-docker-containers/) - Code on [GitHub](https://github.com/mzazon/php-apache-mysql-containerized/)



### LEMP

Stack Overflow - [Alpine variants of PHP and Apache/httpd in Docker](https://stackoverflow.com/questions/41303775/alpine-variants-of-php-and-apache-httpd-in-docker) - Apache/NGINX and PHP with FCGI

New Media Campaigns - [Docker for PHP Developers](https://www.newmediacampaigns.com/blog/docker-for-php-developers) - Nginx, PHP and MySQL

 Java Spring

Cloudreach - [Containerize This! How to Dockerize Your Java Spring Application](https://www.cloudreach.com/blog/dockerize-java-spring-application/) - Uses [multi-stage](https://docs.docker.com/develop/develop-images/multistage-build/) builds



### GoLang

Cloudreach - [Containerize This! How to build Golang Dockerfiles](https://www.cloudreach.com/blog/containerize-this-golang-dockerfiles/) - Uses [multi-stage](https://docs.docker.com/develop/develop-images/multistage-build/) builds



## Advice and Guidance

### Best Practice

Docker Docs - [Docker development best practices](https://docs.docker.com/develop/dev-best-practices/)

Docker Docs - [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

Docker Docs - [Isolate containers with a user namespace](https://docs.docker.com/engine/security/userns-remap/)

Red Hat Developer - [10 things to avoid in docker containers](https://developers.redhat.com/blog/2016/02/24/10-things-to-avoid-in-docker-containers/)

Red Hat Developer - [Keep it small: a closer look at Docker image sizing](https://developers.redhat.com/blog/2016/03/09/more-about-docker-images-size/)

Project Atomic - [Guidance for Docker Image Authors](http://www.projectatomic.io/docs/docker-image-author-guidance/)

Ivan Krizsan - [Time in Docker Containers](https://www.ivankrizsan.se/2015/10/31/time-in-docker-containers/)



### Small Images

A few nice articles relating to Alpine Linux:

- [The 3 Biggest Wins When Using Alpine as a Base Docker Image](https://nickjanetakis.com/blog/the-3-biggest-wins-when-using-alpine-as-a-base-docker-image)
- [Reduce Docker image sizes using Alpine](https://www.sandtable.com/reduce-docker-image-sizes-using-alpine/)
- [Tips & Tricks with Alpine + Docker](http://blog.zot24.com/tips-tricks-with-alpine-docker/)
- [Alpine Dockerfile Advantages of --no-cache Vs. rm /var/cache/apk/*](https://stackoverflow.com/questions/49118579/alpine-dockerfile-advantages-of-no-cache-vs-rm-var-cache-apk)

The latest packages for Alpine Linux can be viewed online:

- [Alpine Linx Packages](https://pkgs.alpinelinux.org/package/v3.10/main/x86_64/python3)

Note: Image size is not the primary consideration when choosing a base OS but it is still quite cool.



