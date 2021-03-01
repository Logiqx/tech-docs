# MySQL

## Background

MySQL is often used as the database component in a [LAMP](https://en.wikipedia.org/wiki/LAMP_(software_bundle)) / [LEMP](https://en.wikipedia.org/wiki/Solution_stack) web application software stack.

I started using MySQL 4.1 as part of the LAMP stack for CAESAR sometime around 2005.



## Versions

### Release History

Release dates have been taken from [Wikipedia](https://en.wikipedia.org/wiki/MySQL#Release_history) and the MySQL release notes.

Legacy Releases:

| Release | General availability | Latest minor version | Latest release | End of support |
| ------- | -------------------- | -------------------- | -------------- | -------------- |
| 4.1     | October 23, 2004     | 4.1.25               | 2008-12-01     | December 2009  |
| 5.0     | October 19, 2005     | 5.0.96               | 2012-03-21     | December 2011  |
| 5.1     | November 14, 2008    | 5.1.73               | 2013-12-03     | December 2013  |

Releases where official images are available on [Docker Hub](https://hub.docker.com/_/mysql):

| Release | General availability | Latest minor version | Latest release | End of support |
| ------- | -------------------- | -------------------- | -------------- | -------------- |
| 5.5     | December 3, 2010     | 5.5.62               | 2018-10-22     | December 2018  |
| 5.6     | February 5, 2013     | 5.6.43               | 2019-01-21     | February 2021  |
| 5.7     | October 21, 2015     | 5.7.25               | 2019-01-21     | October 2023   |
| 8.0     | April 19, 2018       | 8.0.15               | 2019-02-01     | April 2026     |



### My Environments

| Environment | Current Version | Original Version |
| --- | --- | --- |
| VirtualBox - INSPIRON (Logiqx development) | 5.0.51a | [v4.1.17](http://ftp.nchu.edu.tw/MySQL/doc/refman/4.1/en/news-4-1-x.html) (2006-01-27) |
| VirtualBox - WIN7AMP (TLOW development) | [5.5.19](https://dev.mysql.com/doc/relnotes/mysql/5.5/en/) (2011-12-08) | ? |
| XE Solutions - caesar.logiqx.com | [5.5.61](https://dev.mysql.com/doc/relnotes/mysql/5.5/en/) (2018-07-27) | [4.1.13](http://ftp.nchu.edu.tw/MySQL/doc/refman/4.1/en/news-4-1-x.html) (2005-07-15) |
| eUKHost - mikeg.me.uk | [5.6.43](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/) (2019-01-21) |  |
| Docker - Laptop (local development) | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/) (2019-02-01) | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/) (2019-02-01) |



## Docker

### Downloads

[MySQL](https://hub.docker.com/_/mysql) - Official Docker image

[MySQL Server](https://hub.docker.com/r/mysql/mysql-server/) - Optimised Docker image from the MySQL team at Oracle 

[Deploying MySQL on Linux with Docker](https://dev.mysql.com/doc/mysql-installation-excerpt/5.7/en/linux-installation-docker.html) - MySQL Documentation



### Startup

#### Running from the Command Line

Starting MySQL and making it accessible from the host machine - e.g. MySQL Workbench:

```sh
docker run --name mysql -p 3306:3306 -d mysql:tag
```

Running the MySQL client from within a new container:

```sh
docker run -it --link mysql:mysql --rm mysql:tag sh -c 'exec mysql -h"$MYSQL_PORT_3306_TCP_ADDR" -P"$MYSQL_PORT_3306_TCP_PORT" -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD"'
```

Note: This example uses the legacy "link" feature and it would be better to use a user-defined [bridge](https://docs.docker.com/network/bridge/).



#### Running from Docker Compose

A nice way to start MySQL is with Docker Compose, especially if it is part of a LAMP / LEMP stack.

**.env**

```sh
MYSQL_VERSION=8.0
MYSQL_DATABASE=wca
```

**docker-compose.yml**

```yaml
version: '3'
services:
  mysql:
    image: mysql:${MYSQL_VERSION}
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

MySQL parameters can be supplied on the command line, Docker Compose file or in a configuration file.

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
  mysql:
    image: mysql:${MYSQL_VERSION}
    command: --innodb-buffer-pool-size=2G  --innodb-log-file-size=512M
    ...
```

Note: Parameter names in the configuration file use underscores whereas the command line uses hyphens.



#### Docker Log

This section records a few observations that I have made when running MySQL in Docker.

#### mbind

The following error message intermittently appears in the Docker log when running MySQL:

```
mbind: Operation not permitted
```

Note: The mbind error does not appear in the Docker log when using MariaDB.



### Configuration


#### InnoDB Buffer Pool Size

MySQL allocates buffers and caches to improve performance of database operations. The default configuration is designed to permit a MySQL server to start on a virtual machine that has approximately 512MB of RAM. You can improve MySQL performance by increasing the values of certain cache and buffer-related system variables.

One of the most important parameters for MySQL query performance is the InnoDB buffer pool size.

I use a 2GB buffer pool so that MySQL can hold two different versions of the [WCA](https://www.worldcubeassociation.org/results/misc/export.html) database in memory:

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



### Blogs on Tuning

Here are a number of articles that discuss the main parameters that I tweak in MySQL:

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