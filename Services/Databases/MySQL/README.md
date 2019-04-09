# MySQL

## Background

MySQL is often used as the database component in a [LAMP](https://en.wikipedia.org/wiki/LAMP_(software_bundle)) / [LEMP](https://en.wikipedia.org/wiki/Solution_stack) web application software stack.

I started using MySQL 4.1 for CAESAR sometime around 2005.



## Release History

Information from [Wikipedia](https://en.wikipedia.org/wiki/MySQL#Release_history) and release notes:

| Release | General availability | Latest minor version | Latest release | End of support |
| ------- | -------------------- | -------------------- | -------------- | -------------- |
| 4.1     | October 23, 2004     | 4.1.25               | 2008-12-01     | ?              |
| 5.0     | October 19, 2005     | 5.0.96               | 2012-03-21     | ?              |
| 5.1     | November 14, 2008    | 5.1.73               | 2013-12-03     | December 2013  |
| 5.5     | December 3, 2010     | 5.5.62               | 2018-10-22     | December 2018  |
| 5.6     | February 5, 2013     | 5.6.43               | 2019-01-21     | February 2021  |
| 5.7     | October 21, 2015     | 5.7.25               | 2019-01-21     | October 2023   |
| 8.0     | April 19, 2018       | 8.0.15               | 2019-02-01     | April 2026     |



My environments:

| Environment | Current Version | Original Version |
| --- | --- | --- |
| caesar.logiqx.com | [5.5.61](https://dev.mysql.com/doc/relnotes/mysql/5.5/en/) (2018-07-27) | [4.1.13](http://ftp.nchu.edu.tw/MySQL/doc/refman/4.1/en/news-4-1-x.html) (2005-07-15) |
| mikeg.me.uk | [5.6.43](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/) (2019-01-21) | ? |
| VirtualBox | ? | ? |
| Docker | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/>) (2019-02-01) | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/>) (2019-02-01) |



## MariaDB

The day Oracle announced the purchase of Sun, Michael "Monty" Widenius forked MySQL, launching [MariaDB](https://mariadb.com/), and took a swath of MySQL developers with him



## Docker

### Official Images

Docker Hub - [MySQL Server](https://hub.docker.com/r/mysql/mysql-server/)
MySQL Dev - [MySQL Server Deployment with Docker](https://dev.mysql.com/doc/mysql-installation-excerpt/5.7/en/docker-mysql-getting-started.html)



### Manual Setup

Puling the MySQL image from Docker Hub:

```sh
docker pull mysql:8.0
```

Starting the MySQL server:

```sh
docker run --name mysql -p 3306:3306 -d mysql:8.0
```

Running the MySQL client from a new container:

```sh
docker run -it --link mysql:mysql --rm mysql sh -c 'exec mysql -h"$MYSQL_PORT_3306_TCP_ADDR" -P"$MYSQL_PORT_3306_TCP_PORT" -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD"'
```



## Configuration

### Parameters vs Configuration File

etc/mysql/conf.d/docker.cnf

```sh
innodb_buffer_pool_size=2G
secure-file-priv=/home/jovyan/work/wca-ipy/data
```



### Data Import and Export Operations

TODO:

```sh
secure-file-priv=/home/jovyan/work/wca-ipy/data
```



### Memory

TODO:

```sh
innodb_buffer_pool_size=2G
```

Checking buffer pool size (GiB) via SQL:

```sql
SELECT @@innodb_buffer_pool_size/1024/1024/1024;

Result: 2.000000000000
```

Links:

- [How MySQL Uses Memory](https://dev.mysql.com/doc/refman/8.0/en/memory-use.html)
- [Configuring InnoDB Buffer Pool Size](https://dev.mysql.com/doc/refman/8.0/en/innodb-buffer-pool-resize.html)

