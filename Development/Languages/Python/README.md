# Python

[Python](https://www.python.org/) is my "go to" language for most projects.



## Python 2 to Python 3

I have made some notes detailing some common code changes required when migrating from Python 2 to [Python 3](Python3.md).



## Docker Images

I have a number of GitHub projects which have been developed using [Jupyter Notebook](https://jupyter.org/) and deployed using regular [Python](https://www.python.org/).

Examining my Docker files and Docker Compose files provides a useful reference as to how this can be achieved.

### Development

My wca-db project uses Docker Compose for Jupyter Notebook and MariaDB.

The Docker Compose files are within the [docker](https://github.com/Logiqx/wca-db/tree/master/docker) directory of the [project](https://github.com/Logiqx/wca-db) on GitHub.

Base Images:

- Jupyter Docker Stacks - [GitHub](https://github.com/jupyter/docker-stacks) + [Docker Hub](https://hub.docker.com/u/jupyter/)
- Minimal Jupyter Notebook Stack [GitHub](https://github.com/jupyter/docker-stacks/tree/master/minimal-notebook)  + [Docker Hub](https://hub.docker.com/r/jupyter/minimal-notebook/)

### Deployment

A number of my projects are developed with Jupyter Notebook but deployed as regular Python.

The Dockerfile in the project root uses a multi-stage build to convert the .ipynb files to regular .py files and create a lightweight Python image for deployment.

GitHub repositories:

- [wca-db](https://github.com/Logiqx/wca-db) - World Cube Association - Scripts to download and optimise the database
- [wca-ipy](https://github.com/Logiqx/wca-ipy) - iPython notebooks to produce (un)official rankings for the senior cubing community

The base image for deployment is on DockerHub:

- [python-bs4](https://hub.docker.com/repository/docker/logiqx/python-bs4) - Python 3 + Beautiful Soup 4 on Alpine Linux

The hardest bit of the python-bs4 build relates to [lxml](https://lxml.de/installation.html) but it is fully documented on [GitHub](https://github.com/Logiqx/python-bs4).

BeautifulSoup and lxml can quickly be tested using the following Python script:

```
from bs4 import BeautifulSoup
soup = BeautifulSoup("<html/>", "lxml")
```

