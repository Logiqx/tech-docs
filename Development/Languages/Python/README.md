# Python

[Python](https://www.python.org/) is my "go to" language.



## Docker

Build

```
docker build --tag=mynotebook .
```



Minimal Notebook:

```sh
docker pull jupyter/minimal-notebook

# docker run --name notebook --mount source=scratch,target=/home/jovyan/work -p 8888:8888 -d jupyter/minimal-notebook

docker run --name notebook --mount type=bind,source="C:/Mounts/Jupyter",target=/home/jovyan/work -p 8888:8888 -d jupyter/minimal-notebook

docker start notebook
docker logs notebook	(look for "Copy/paste this URL into your browser")
docker stop notebook
```

Install Libraries:

```sh
docker exec notebook pip install beautifulsoup4
docker exec notebook pip install lxml
```

PyMySQL:

```sh
docker exec notebook conda install pymysql

docker run --name notebook2 --link mysql:mysql --mount source=scratch,target=/home/jovyan/scratch -p 8888:8888 -d jupyter/minimal-notebook
```

GitHub:

```sh
docker exec notebook git clone https://github.com/Logiqx/wca-ipy.git scratch/wca-ipy
docker exec notebook git clone https://github.com/Logiqx/wca-spv.git scratch/wca-spv
docker exec notebook ls -al scratch

# docker exec notebook git --git-dir=scratch/wca-ipy/.git --work-tree=scratch/wca-ipy status

docker exec notebook sh -c 'cd work/wca-ipy && git status'
docker exec notebook sh -c "cd work/wca-ipy && git checkout -- 'Over 40s.ipynb'"
docker exec notebook sh -c 'cd work/wca-ipy && git pull'
```

References:

GitHub - [Jupyter Docker Stacks](https://github.com/jupyter/docker-stacks)
GitHub - [Minimal Jupyter Notebook Stack](https://github.com/jupyter/docker-stacks/tree/master/minimal-notebook)



## Python 2 to Python 3

This section summarises the changes required to convert [wca-ipy](https://github.com/Logiqx/wca-ipy) from Python 2 to Python 3.

References:

* [Porting Python 2 Code to Python 3](https://docs.python.org/3/howto/pyporting.html)



### General

#### Print

print -> print()

#### Integer Division

/ -> //

#### Dictionaries

eventsDict.has_key(inputRow[0]) -> inputRow[0] in eventsDict



### Libraries

#### URLLib

urllib2 -> urllib

#### CSV Reader

with open(fn, 'rb') -> with open(fn, 'r')

#### CSV Writer

with open(fn, 'wb') -> with open(fn, 'w')
csv.writer(f, quoting = csv.QUOTE_MINIMAL) -> csv.writer(f, quoting = csv.QUOTE_MINIMAL, lineterminator = os.linesep)
