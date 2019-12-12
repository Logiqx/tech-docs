# Building Images

## Checklist

Here is my personal checklist for building high quality Docker images.

Further details about every topic can be found elsewhere in this document.

### Image Size

#### Multi-Stage Builds

Use multi-stage to keep build tools and other clutter out of the final image, ensuring the final image is kept small.

#### Chain RUN Commands

Chain `RUN` commands using the `&&` syntax to reduce the number of layers and exclude ephemeral data / files from the final image.

#### Package Managers

Alpine: Use `apk add --no-cache` which is the simple alternative to`apk update && apk add <packages>` followed by `rm -fr /var/cache/apk/*`. Both approaches remove the APK cache from the image.

Debian / Ubuntu: Use `apt-get update && apt-get install -y --no-install-recommends <packages>` followed by `rm -rf /var/lib/apt/lists/*`.

Python: Use `pip install --no-cache-dir <packages>`.



### Image Management

#### Tags

Ensure that images have unique tags whether they be of the form `X.Y.Z`, `YYYYMMDD` or `XXXXXXXXXXXX`.

Failure to use unique tags leads to old images being listed as `<none>` when they are superseded.

#### Environment Variables

The `ENV` directive can is an effective way to pass important parameters into containers when they are started up.

#### Volumes

Use volumes for folders that need to be persistent or shared with other containers. Ideally the container should be runnable as "read-only" meaning that no data is written to the container's own layer.

#### Work Directory

Specifying `WORKDIR` is helpful as it will put the user into the appropriate directory by default.

#### BuildKit

Use BuildKit for faster builds (especially multi-stage), better caching and some additional features.



### Container Security

#### Non-Root User

Create a dedicated user (and maybe a group) to the image and switch to it with the `USER` command.

Be sure to `chown` and `chmod` the contents of the home directory. Also note that the default permissions may be inconsistent between Debian / Ubuntu and Alpine.



### Container Reliability

#### Specify Version

Be sure to specify version numbers to avoid breaking changes in the future.

Debian / Ubuntu: `apt-get install -y --no-install-recommends tini=0.18.*`

Alpine: `apk add --no-cache tini=~0.18`

Python: `pip install --no-cache-dir beautifulsoup4==4.8.*`

#### Init Process

Use *tini* or *dumb-init* as the `ENTRYPOINT` to ensure containers can be gracefully shut down and avoid zombies which can exhaust PIDs and other resources on the host.

#### Health Check

Incorporate a simple health check to ensure that services running in containers can be reliably monitored.



## General

### Multi-stage Builds

Using a [multi-stage build](https://docs.docker.com/develop/develop-images/multistage-build/) to convert a Notebook to a simple Python script is highly advantageous.

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



### Docker ENTRYPOINT vs CMD

[Dockerfile: ENTRYPOINT vs CMD](https://www.ctl.io/developers/blog/post/dockerfile-entrypoint-vs-cmd/) is a nice article describing the differences.

TL;DR - ENTRYPOINT is always run and CMD can be overridden.



## The PID 1 Problem

It took me a little while of using Docker before I realised the benefits of a lightweight init process (e.g. "tini" or "dumb-init") in a Docker image.

TL;DR - Use tini or dumb-init as the ENTRYPOINT to ensure that containers can be gracefully stopped and do not cause issues relating to zombies on the Docker host.

[Docker and the PID 1 zombie reaping problem](https://blog.phusion.nl/2015/01/20/docker-and-the-pid-1-zombie-reaping-problem/amp/) provides a very thorough description of the problem

### Popular Solutions

#### Tini

[What is advantage of Tini?](https://github.com/krallin/tini/issues/8#issuecomment-146135930) is a through description by the author of why tini is required.

#### --init

[How to use --init parameter in docker run](https://stackoverflow.com/questions/43122080/how-to-use-init-parameter-in-docker-run) explains why --init is basically the same as running tini.

#### dumb-init

[Introducing dumb-init, an init system for Docker containers](https://engineeringblog.yelp.com/2016/01/dumb-init-an-init-for-docker.html) is a great article that describes the proplems that dumb-init (and tini) resolve.

[How critical is dumb-init for Docker?](https://stackoverflow.com/questions/37374310/how-critical-is-dumb-init-for-docker) includes comments from the author of tini, impartially comparing dumb-init and tini.



## Automation

It is easy to automate builds on DockerHub so that changes to a git repository trigger an image build.

[Docker Hub: Configure Automated Builds from GitHub and BitBucket](https://docs.docker.com/docker-hub/builds/link-source/)



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



