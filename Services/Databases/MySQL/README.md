# MySQL

## Background

MySQL is often used as the database component in a [LAMP](https://en.wikipedia.org/wiki/LAMP_(software_bundle)) / [LEMP](https://en.wikipedia.org/wiki/Solution_stack) web application software stack.

I started using MySQL 4.1 for CAESAR sometime around 2005.

## MariaDB

The day Oracle announced the purchase of Sun, Michael "Monty" Widenius forked MySQL, launching [MariaDB](https://mariadb.com/), and took a swath of MySQL developers with him.

MariaDB - [All Releases](https://downloads.mariadb.org/mariadb/+releases/)

Docker Hub - [MariaDB](https://hub.docker.com/_/mariadb)

https://mariadb.com/kb/en/library/mariadb-vs-mysql-features/

## Release History

Release information taken from [Wikipedia](https://en.wikipedia.org/wiki/MySQL#Release_history) and release notes.

Legacy Releases:

| Release | General availability | Latest minor version | Latest release | End of support |
| ------- | -------------------- | -------------------- | -------------- | -------------- |
| 4.1     | October 23, 2004     | 4.1.25               | 2008-12-01     | December 2009  |
| 5.0     | October 19, 2005     | 5.0.96               | 2012-03-21     | December 2011  |
| 5.1     | November 14, 2008    | 5.1.73               | 2013-12-03     | December 2013  |

Available on Docker Hub:

| Release | General availability | Latest minor version | Latest release | End of support |
| ------- | -------------------- | -------------------- | -------------- | -------------- |
| **5.5** | December 3, 2010     | 5.5.62               | 2018-10-22     | December 2018  |
| **5.6** | February 5, 2013     | 5.6.43               | 2019-01-21     | February 2021  |
| 5.7     | October 21, 2015     | 5.7.25               | 2019-01-21     | October 2023   |
| **8.0** | April 19, 2018       | 8.0.15               | 2019-02-01     | April 2026     |

My environments:

| Environment | Current Version | Original Version |
| --- | --- | --- |
| caesar.logiqx.com | [5.5.61](https://dev.mysql.com/doc/relnotes/mysql/5.5/en/) (2018-07-27) | [4.1.13](http://ftp.nchu.edu.tw/MySQL/doc/refman/4.1/en/news-4-1-x.html) (2005-07-15) |
| mikeg.me.uk | [5.6.43](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/) (2019-01-21) | ? |
| INSPIRON | 5.0.51a |  |
| WIN7AMP | [5.5.19](https://dev.mysql.com/doc/relnotes/mysql/5.5/en/) (2011-12-08) | ? |
| Docker | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/>) (2019-02-01) | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/>) (2019-02-01) |

## Docker

### Official Images

Docker Hub - [MySQL Server](https://hub.docker.com/r/mysql/mysql-server/)

MySQL Dev - [MySQL Server Deployment with Docker](https://dev.mysql.com/doc/mysql-installation-excerpt/5.7/en/docker-mysql-getting-started.html)

### Manual Startup

Starting the MySQL server:

```sh
docker run --name mysql -p 3306:3306 -d mysql:8.0
```

Running the MySQL client from a new container:

```sh
docker run -it --link mysql:mysql --rm mysql sh -c 'exec mysql -h"$MYSQL_PORT_3306_TCP_ADDR" -P"$MYSQL_PORT_3306_TCP_PORT" -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD"'
```

### Docker Compose

The nicest way to start MySQL is with Docker Compose:

```yaml
version: '3'
services:
  mysql:
    image: mysql:${MYSQL_VERSION}
    command: --innodb-buffer-pool-size=2G --secure-file-priv=/home/mike/extract
    environment:
      MYSQL_DATABASE: ${MYSQL_DATABASE}
      MYSQL_RANDOM_ROOT_PASSWORD: "true"
    ports:
      - "3306:3306"
    volumes:
      - ${PROJECT_ROOT}:/home/mike/extract
      - db:/var/lib/mysql
volumes:
  db:
```

## Configuration

### Parameters vs Configuration File

MySQL parameters can be supplied on the command line or in a MySQL configuration file:

Example configuration file for inclusion in a Docker image - /etc/mysql/conf.d/docker.cnf

```sh
[mysqld]
innodb_buffer_pool_size=2G
secure-file-priv=/home/jovyan/work/wca-ipy/data
```

Example portion of docker-compose.yml

```yaml
version: '3'
services:
  mysql:
    image: mysql:${MYSQL_VERSION}
    command: --innodb-buffer-pool-size=2G  --secure-file-priv=/home/mike/extract
    ...
```

### Memory

One of the most important parameters for MySQL is the InnoDB buffer pool size.

I have chosen to use a 2GB buffer pool so that MySQL can hold the [WCA database](https://www.worldcubeassociation.org/results/misc/export.html) in memory:

```sh
innodb_buffer_pool_size=2G
```

Checking the buffer pool size (GiB) via SQL:

```sql
SHOW VARIABLES LIKE "innodb_buffer_pool_size";

SELECT @@innodb_buffer_pool_size / 1024 / 1024 / 1024;  -- Show size in GB
SELECT @@innodb_buffer_pool_size / 1024 / 1024 / 128;   -- Show size relative to default

SHOW ENGINE INNODB STATUS;
```

Official links:

- [Optimising Memory Use - How MySQL Uses Memory](https://dev.mysql.com/doc/refman/8.0/en/memory-use.html)
- [The InnoDB Storage Engine - Buffer Pool](https://dev.mysql.com/doc/refman/8.0/en/innodb-buffer-pool.html)
- [Configuring InnoDB Buffer Pool Size](https://dev.mysql.com/doc/refman/8.0/en/innodb-buffer-pool-resize.html)
- [InnoDB Startup Options and System Variables](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size)

Blogs / Articles:

- [Calculating InnoDB Buffer Pool Size for Your MySQL Server](https://dzone.com/articles/calculating-innodb-buffer-pool-size-for-your-mysql)
- [How to allocate innodb_buffer_pool_size in MySQL?](https://www.rathishkumar.in/2017/01/how-to-allocate-innodb-buffer-pool-size-in-mysql.html?m=1)

### Data Import and Export Operations

To use "SELECT ... INTO OUTFILE ..." it is typically necessary to update the secure-file-priv variable:

```sh
secure-file-priv=/home/mike/extract
```

Checking the secure file privileges variable:

```sql
SHOW VARIABLES LIKE "secure_file_priv";

SELECT @@secure_file_priv;
```

Official links:

- [Security-Related mysqld Options and Variables](https://dev.mysql.com/doc/refman/8.0/en/security-options.html)
- [Server Command Options](https://dev.mysql.com/doc/refman/8.0/en/server-options.html#option_mysqld_secure-file-priv)