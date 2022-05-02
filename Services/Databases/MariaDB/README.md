# MariaDB

## Background

The day Oracle announced the purchase of Sun, Michael "Monty" Widenius forked MySQL, launching [MariaDB](https://mariadb.com/), and took a swath of MySQL developers with him.

MariaDB is often used as the database component in a [LAMP](https://en.wikipedia.org/wiki/LAMP_(software_bundle)) / [LEMP](https://en.wikipedia.org/wiki/Solution_stack) web application software stack and I have been using it for this purpose since early 2019.



## Versions

### Release History

[MariaDB](https://downloads.mariadb.org/mariadb/+releases/) - All Releases



### My Environments

| Environment                         | Current Version                                              | Original Version                                             |
| ----------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Docker - Laptop (local development) | [10.3.14](https://downloads.mariadb.org/mariadb/+releases/) (2019-04-02) | [10.3.14](https://downloads.mariadb.org/mariadb/+releases/) (2019-04-02) |



## Docker

### Downloads

[MariaDB](https://hub.docker.com/_/mariadb) - Official Docker image

[MariaDB Server](https://hub.docker.com/r/mariadb/server/) - Docker image from the MariaDB team

[Installing and Using MariaDB via Docker](https://mariadb.com/kb/en/library/installing-and-using-mariadb-via-docker/) - MariaDB Documentation



### Startup

#### Running from the Command Line

Starting MariaDB and making it accessible from the host machine - e.g. MySQL Workbench:

```sh
docker run --name mariadb -p 3306:3306 -d mariadb:tag
```

Running the MariaDB client from within a new container:

```sh
docker run -it --link mariadb:mariadb --rm mariadb:tag sh -c 'exec mysql -h"$MYSQL_PORT_3306_TCP_ADDR" -P"$MYSQL_PORT_3306_TCP_PORT" -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD"'
```

Note: This example uses the legacy "link" feature and it would be better to use a user-defined [bridge](https://docs.docker.com/network/bridge/).



#### Running from Docker Compose

A nice way to start MariaDB is with Docker Compose, especially if it is part of a LAMP / LEMP stack.

**.env**

```sh
MARIADB_VERSION=10.3
MYSQL_DATABASE=wca
```

**docker-compose.yml**

```yaml
version: '3'
services:
  mariadb:
    image: mariadb:${MARIADB_VERSION}
    environment:
      MYSQL_DATABASE: ${MYSQL_DATABASE}
      MYSQL_RANDOM_ROOT_PASSWORD: "true"
    ports:
      - "3306:3306"
    volumes:
      - ./mysql/custom.cnf:/etc/mysql/conf.d/my.cnf
      - db:/var/lib/mysql
volumes:
  db:
```

**Command line**

```sh
docker-compose up -d
docker-compose logs
docker-compose stop
docker-compose start
docker-compose down
```



#### Command Line Parameters vs Configuration File

MariaDB parameters can be supplied on the command line, Docker Compose file or in a configuration file.

A configuration file may be bind mounted into the container or built into a bespoke Docker image.

Example configuration file - /etc/mysql/conf.d/my.cnf:

```sh
[mysqld]
innodb_buffer_pool_size        = 2G
innodb_log_file_size           = 512M
```

Portion of docker-compose.yml to illustrate the same parameters:

```yaml
version: '3'
services:
  mariadb:
    image: mariadb:${MARIADB_VERSION}
    command: --innodb-buffer-pool-size=2G  --innodb-log-file-size=512M
    ...
```

Note: Parameter names in the configuration file use underscores whereas the command line uses hyphens.



### Configuration

Note: Most of the references below are actually for MySQL but are equally applicable to MariaDB.



#### InnoDB Buffer Pool Size

MariaDB allocates buffers and caches to improve performance of database operations. The default configuration is designed to permit a MariaDB server to start on a virtual machine that has approximately 512MB of RAM. You can improve MariaDB performance by increasing the values of certain cache and buffer-related system variables.

One of the most important parameters for MariaDB query performance is the InnoDB buffer pool size.

I use a 2GB buffer pool so that MariaDB can hold two different versions of the [WCA](https://www.worldcubeassociation.org/results/misc/export.html) database in memory:

```sh
innodb_buffer_pool_size=2G
```

Checking the buffer pool size via SQL:

```sql
SHOW VARIABLES LIKE "innodb_buffer_pool_size";

SELECT @@innodb_buffer_pool_size / 1024 / 1024 / 1024;  -- Show size in GiB
SELECT @@innodb_buffer_pool_size / 1024 / 1024 / 128;   -- Show size relative to default
```

Checking how much of the buffer pool is being used:

```sql
SHOW ENGINE INNODB STATUS;

SELECT CONCAT(FORMAT(pages_data * 100.0 / pages_total, 2), ' %') AS buffer_pool_pct_used
FROM
(
	SELECT variable_value AS pages_data
	FROM information_schema.global_status
	WHERE variable_name = 'Innodb_buffer_pool_pages_data'
) AS t1,
(
	SELECT variable_value AS pages_total
	FROM information_schema.global_status
	WHERE variable_name = 'Innodb_buffer_pool_pages_total'
) AS t2;
```

References:

[How MySQL Uses Memory](https://dev.mysql.com/doc/refman/8.0/en/memory-use.html) - Includes a summary of of the InnoDB buffer pool

[Optimizing InnoDB Disk I/O](https://dev.mysql.com/doc/refman/8.0/en/optimizing-innodb-diskio.html) - Increase buffer pool size  (recommends 50 to 75 percent of system memory)

[InnoDB Buffer Pool](https://dev.mysql.com/doc/refman/8.0/en/innodb-buffer-pool.html) - Detailed description of the InnoDB buffer pool, configuration and monitoring

[Configuring InnoDB Buffer Pool Size](https://dev.mysql.com/doc/refman/8.0/en/innodb-buffer-pool-resize.html) - Further information about configuration of the InnoDB buffer pool

[Calculating InnoDB Buffer Pool Size for Your MySQL Server](https://dzone.com/articles/calculating-innodb-buffer-pool-size-for-your-mysql) - Database Zone Tutorial

[How to allocate innodb_buffer_pool_size in MySQL?](https://www.rathishkumar.in/2017/01/how-to-allocate-innodb-buffer-pool-size-in-mysql.html?m=1) - Technology Blog

[InnoDB Startup Options and System Variables](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size) - innodb_buffer_pool_size



#### InnoDB Log File Size

The InnoDB log file size can affect the performance when loading large tables.

There are multiple log files (default = 2) so the total size is determined by multiplying the file size and the number of files:

```sh
innodb_log_file_size=512M
innodb_log_files_in_group=2
```

References:

[Optimizing InnoDB Redo Logging](https://dev.mysql.com/doc/refman/8.0/en/optimizing-innodb-logging.html) - "Make your redo log files big, even as big as the buffer pool"

[How to size InnoDB Log Buffer Size](http://www.mysqlab.net/knowledge/kb/detail/topic/innodb/id/6553) - Mentions the global status InnoDB log waits

[MySQL transaction size - How Big is Too Big?](https://dba.stackexchange.com/questions/19569/mysql-transaction-size-how-big-is-too-big) - One bottleneck to be aware of is the InnoDB Log Buffer

[How to Calculate a Good InnoDB Log File Size](https://www.percona.com/blog/2008/11/21/how-to-calculate-a-good-innodb-log-file-size/) - Big enough to let InnoDB optimize its I/O

[InnoDB Startup Options and System Variables](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_log_file_size) - innodb_log_file_size



#### Data Import and Export Operations

To use "SELECT ... INTO OUTFILE ..." it is necessary to update the secure_file_priv variable.

This parameter can specify a specific path or allow reading / writing in any path by omitting the value:

```sh
secure_file_priv=
```

Reference:

[Server Command Options](https://dev.mysql.com/doc/refman/8.0/en/server-options.html#option_mysqld_secure-file-priv) - secure_file_priv



#### InnoDB Flush Log at Transaction Commit

This parameter controls the balance between strict ACID compliance for commit operations and higher performance that is possible when commit-related I/O operations are rearranged and done in batches.

You can achieve better performance by changing the default value but then you can lose transactions in a crash. This reduced my typical load times in MariaDB from ~5 minutes to under 1 minute 30 seconds.

```sh
innodb_flush_log_at_trx_commit=2
```

Reference:

[InnoDB Startup Options and System Variables](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_flush_log_at_trx_commit) - innodb_flush_log_at_trx_commit



#### InnoDB Flush Method

The method used to flush data to InnoDB data files and log files can significantly affect I/O throughput.

When running MariaDB in Docker for Windows this setting halved my database load times.

```sh
innodb_flush_method=O_DIRECT_NO_FSYNC
```

References:

[Optimizing InnoDB Disk I/O](https://dev.mysql.com/doc/refman/8.0/en/optimizing-innodb-diskio.html) - Adjust the flush method (recommends benchmarks)

[Clarification on MySQL innodb_flush_method variable](https://dba.stackexchange.com/questions/1568/clarification-on-mysql-innodb-flush-method-variable/1575#1575) - Explanation on fdatasync() vs fsync()

[InnoDB Startup Options and System Variables](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_flush_method) - innodb_flush_method



#### InnoDB Doublewrite Buffer

A number of blogs refer to performance improvements when disabling the doublewrite buffer as below.

I haven't observed any improvement with this change on my system so I do not disable it on my laptop.

Note: This parameter may not be having any effect due to the "flush" related parameters described earlier.

```sh
innodb_doublewrite=OFF
```

References:

[InnoDB Disk I/O](https://dev.mysql.com/doc/refman/8.0/en/innodb-disk-io.html) - Read-Ahead and Doublewrite Buffer

[InnoDB Startup Options and System Variables](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_doublewrite) - innodb_doublewrite



#### InnoDB Native AIO

After experimentation, I've ended up leaving the asynchronous I/O subsystem (native AIO) enabled.

Switching it off did not appear to have any discernible impact when running MariaDB in Docker.

```sh
innodb_use_native_aio=ON
```

References:

[Optimizing InnoDB Disk I/O](https://dev.mysql.com/doc/refman/8.0/en/optimizing-innodb-diskio.html) - InnoDB uses the asynchronous I/O subsystem (native AIO) on Linux

[Using Asynchronous I/O on Linux](https://dev.mysql.com/doc/refman/8.0/en/innodb-linux-native-aio.html) - Detailed description of asynchronous I/O

[InnoDB Startup Options and System Variables](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_use_native_aio) - innodb_use_native_aio



#### InnodDB Log Buffer Size

After experimenting with various log buffer sizes (smaller and larger), I've ended up using the default.

```sh
innodb_log_buffer_size=8M
```

It is possible to determine if the log buffer is too small using the following SQL:

```sql
SHOW GLOBAL STATUS LIKE 'innodb_log_waits';
```

If the number of log waits is greater than 0 it implies that the log buffer is potentially too small.

References:

[Optimizing InnoDB Redo Logging](https://dev.mysql.com/doc/refman/8.0/en/optimizing-innodb-logging.html) - Consider increasing the size of the log buffer

[InnoDB Startup Options and System Variables](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_log_buffer_size) - innodb_log_buffer_size



#### Auto Commit

Some types of load can be speeded up by disabling auto commit.

This approach has little to no impact when loading the WCA database.

```sql
SET autocommit=0;
... SQL import statements ...
COMMIT;
```

Reference:

[Bulk Data Loading for InnoDB Tables](https://dev.mysql.com/doc/refman/5.5/en/optimizing-innodb-bulk-data-loading.html) - When importing data into InnoDB, turn off autocommit mode



### Resetting the root Password

This approach is taken from a [gist](https://gist.github.com/rordi/ab55c92848fd06884172ed22ae88d7df):

Override the entrypoint in docker-compose.yml for the MariaDB Docker container by adding:

    entrypoint: mysqld_safe --skip-grant-tables --user=mysql

The start up the Docker Compose stack:

    $> docker-compose up -d

Then login to the Docker container:

    $> docker exec -ti docker-container-name bash

And login as root without password:

    $> mysql -u root -p

Change the root password in mysql cli:

    mysql> FLUSH PRIVILEGES;
    mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'your_secret';
    mysql> FLUSH PRIVILEGES;

Logout of mysql and the Docker container (2x exit), remove the entrypoint line from the docker-compose.yml and reload the Docker Composer stack:

    $> docker-compose up -d

You can now login to the MariaDB container and connect to the database with the new root password:

    $> docker exec -ti docker-container-name bash
    $> mysql -u root -p



### Troubleshooting

Once in while a transaction might be aborted and leave a table in a broken state.

This will sometimes be apparent when looking at the Docker log and cannot be fixed by simply dropping the table.

```
2021-03-02 11:54:21 0 [ERROR] InnoDB: Operating system error number 2 in a file operation.
2021-03-02 11:54:21 0 [ERROR] InnoDB: The error means the system cannot find the path specified.
2021-03-02 11:54:21 0 [ERROR] InnoDB: If you are installing InnoDB, remember that you must create directories yourself, InnoDB does not create them.
2021-03-02 11:54:21 0 [ERROR] InnoDB: Cannot open datafile for read-only: './wca/users_20190507.ibd' OS error: 71
2021-03-02 11:54:21 0 [ERROR] InnoDB: Operating system error number 2 in a file operation.
2021-03-02 11:54:21 0 [ERROR] InnoDB: The error means the system cannot find the path specified.
2021-03-02 11:54:21 0 [ERROR] InnoDB: If you are installing InnoDB, remember that you must create directories yourself, InnoDB does not create them.
2021-03-02 11:54:21 0 [ERROR] InnoDB: Could not find a valid tablespace file for wca`.`users_20190507. Please refer to https://mariadb.com/kb/en/innodb-data-dictionary-troubleshooting/ for how to resolve the issue.
```

Under such circumstances you can check the information schema for the presence table:

```
SELECT * FROM INFORMATION_SCHEMA.INNODB_SYS_TABLES WHERE NAME LIKE '%users_20190507%';
```

You can "fix" the table such that it can be dropped by ensuring there is a suitable ".ibd" file to match the ".frm".

```
docker run -it --rm -v wca_mariadb:/var/lib/mysql ubuntu:bionic bash
cd /var/lib/mysql
cp -p wca_dev/users.ibd wca/users_20190507.ibd
cp -p wca_dev/users.frm wca/users_20190507.frm
```

Note: You could use `docker exec -it wca_mariadb_1 bash` instead of "docker run" but a new container is a good habit.

Once the ".ibd" and ".frm" are present you can restart MariaDB then drop the table via SQL.



### Blogs on Tuning

Here are a number of articles that discuss the main parameters that I tweak in MariaDB:

[Configuring MariaDB for Optimal Performance](https://mariadb.com/kb/en/library/configuring-mariadb-for-optimal-performance/) - article to help configure MariaDB for optimal performance

[InnoDB Startup Configuration](https://dev.mysql.com/doc/refman/8.0/en/innodb-init-startup-configuration.html) - buffer pool size and redo log file size

[Dedicated MySQL Server](https://dev.mysql.com/doc/refman/8.0/en/innodb-dedicated-server.html) - buffer pool size, redo log file size and flush method (O_DIRECT_NO_FSYNC)

[Performance Tuning After Installation](https://www.percona.com/blog/2016/10/12/mysql-5-7-performance-tuning-immediately-after-installation/) - buffer pool size, redo log file size, flush at commit and flush method

[InnoDB Performance Optimization](https://www.percona.com/blog/2013/09/20/innodb-performance-optimization-basics-updated/) - buffer pool size, redo log file size, flush at commit and flush method

[Restoring a big MySQL database](http://vrodic.blogspot.com/2017/08/how-to-have-higher-chance-of-success.html) - buffer pool size, redo log file size, flush at commit and flush method



## MySQL vs MariaDB

I have found that MariaDB outperforms MySQL for my workloads when running in Docker on my Laptop.

Loading the WCA database takes about 5 minutes on MySQL but around 1.5 minutes on MariaDB.

[MySQL Performance: MySQL vs. MariaDB](https://www.liquidweb.com/kb/mysql-performance-mysql-vs-mariadb/) - great comparison of MySQL and MariaDB

[MariaDB versus MySQL - Features](https://mariadb.com/kb/en/library/mariadb-vs-mysql-features/) - feature comparison