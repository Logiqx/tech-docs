# Linux Containers on Windows

## Background

Linux containers with Hyper-V isolation run each Linux container (LCOW) in an optimized Linux VM with just enough OS to run containers. In contrast to the Moby VM approach, each LCOW has its own kernel and its own VM sandbox. They're also managed by Docker on Windows directly.

I have experimented with Linux Containers on Windows (LCOW) on Docker 18.09.2.

Until the main issues have been fixed, I will continue to run Linux containers using the Moby VM approach.

Note: Enable in Docker for Windows via Docker Settings -> Daemon -> Experimental features.



### Links

Docker Blog - [Docker for WIndows 18.02 with Windows 10 Fall Creators Update](https://blog.docker.com/2018/02/docker-for-windows-18-02-with-windows-10-fall-creators-update/)

Microsoft Docs - [Linux Containers on Windows](https://docs.microsoft.com/en-us/virtualization/windowscontainers/deploy-containers/linux-containers)



## Issues

#### Containers will not start from Pengwin - P3

Bind mounts fail when starting containers from Pengwin via the Docker bridge.

This isn't a showstopper because these containers / services can still be started from Powershell.

#### Containers are slow to start - P3

Containers running in a dedicated Linux VM take slightly longer to start.

This is an acceptable trade-off for the benefits that Hyper-V isolation provide.

#### Containers cannot be stopped - P2

It does not seem possible to stop containers cleanly. The "stop" operation times out after 300 seconds (i.e. 5 minutes) and resorts to killing the container. It does not even appear possible to attach to the container interactively using "docker attach" to use ctrl-c.

Ultimately this isn't disastrous for me because I consider my containers to be immutable and ephemeral. Killing the containers using "docker kill -s 9" is an acceptable workaround in a development environment.

GitHub issue - [Linux containers take too long to stop when executed with LCOW switch on](https://github.com/moby/moby/issues/37919)

#### Containers cannot run MySQL or MariaDB - P1

These are the show stoppers for me since I use Docker for my databases.

The logs for MariaDB and MySQL show issue with exclusive locks.

**MariaDB**

```
2019-04-12 11:25:15 0 [Note] mysqld (mysqld 10.3.14-MariaDB-1:10.3.14+maria~bionic) starting as process 1 ...
2019-04-12 11:25:15 0 [Warning] Setting lower_case_table_names=2 because file system for /var/lib/mysql/ is case insensitive
2019-04-12 11:25:15 0 [ERROR] mysqld: Can't lock aria control file '/var/lib/mysql/aria_log_control' for exclusive use, error: 95. Will retry for 30 seconds
2019-04-12 11:25:46 0 [ERROR] mysqld: Got error 'Could not get an exclusive lock; file is probably in use by another process' when trying to use aria control file '/var/lib/mysql/aria_log_control'
2019-04-12 11:25:46 0 [ERROR] Plugin 'Aria' init function returned error.
2019-04-12 11:25:46 0 [ERROR] Plugin 'Aria' registration as a STORAGE ENGINE failed.
2019-04-12 11:25:46 0 [Note] InnoDB: Using Linux native AIO
2019-04-12 11:25:46 0 [Note] InnoDB: Mutexes and rw_locks use GCC atomic builtins
2019-04-12 11:25:46 0 [Note] InnoDB: Uses event mutexes
2019-04-12 11:25:46 0 [Note] InnoDB: Compressed tables use zlib 1.2.11
2019-04-12 11:25:46 0 [Note] InnoDB: Number of pools: 1
2019-04-12 11:25:46 0 [Note] InnoDB: Using SSE2 crc32 instructions
2019-04-12 11:25:46 0 [Note] InnoDB: Initializing buffer pool, total size = 2G, instances = 8, chunk size = 128M
2019-04-12 11:25:47 0 [Note] InnoDB: Completed initialization of buffer pool
2019-04-12 11:25:47 0 [Note] InnoDB: If the mysqld execution user is authorized, page cleaner thread priority can be changed. See the man page of setpriority().
2019-04-12 11:25:47 0 [ERROR] InnoDB: Unable to lock ./ibdata1 error: 95
2019-04-12 11:25:47 0 [Note] InnoDB: Retrying to lock the first data file
2019-04-12 11:25:48 0 [ERROR] InnoDB: Unable to lock ./ibdata1 error: 95
2019-04-12 11:25:49 0 [ERROR] InnoDB: Unable to lock ./ibdata1 error: 95
2019-04-12 11:25:50 0 [ERROR] InnoDB: Unable to lock ./ibdata1 error: 95
...
```

**MySQL**

```
Initializing database
2019-04-12T11:32:17.222302Z 0 [Warning] [MY-010139] [Server] Changed limits: max_open_files: 1024 (requested 8161)
2019-04-12T11:32:17.222317Z 0 [Warning] [MY-010142] [Server] Changed limits: table_open_cache: 431 (requested 4000)
2019-04-12T11:32:17.222908Z 0 [Warning] [MY-011070] [Server] 'Disabling symbolic links using --skip-symbolic-links (or equivalent) is the default. Consider not using this option as it' is deprecated and will be removed in a future release.
2019-04-12T11:32:17.223602Z 0 [System] [MY-013169] [Server] /usr/sbin/mysqld (mysqld 8.0.15) initializing of server in progress as process 33
2019-04-12T11:32:17.242848Z 0 [Warning] [MY-010159] [Server] Setting lower_case_table_names=2 because file system for /var/lib/mysql/ is case insensitive
2019-04-12T11:32:19.045165Z 1 [ERROR] [MY-012574] [InnoDB] Unable to lock ./ibdata1 error: 95
2019-04-12T11:32:19.046393Z 1 [ERROR] [MY-012592] [InnoDB] Operating system error number 95 in a file operation.
2019-04-12T11:32:19.046415Z 1 [ERROR] [MY-012596] [InnoDB] Error number 95 means 'Operation not supported'
2019-04-12T11:32:19.046479Z 1 [ERROR] [MY-012215] [InnoDB] Cannot open datafile './ibdata1'
2019-04-12T11:32:19.046604Z 1 [ERROR] [MY-012959] [InnoDB] Could not open or create the system tablespace. If you tried to add new data files to the system tablespace, and it failed here, you should now edit innodb_data_file_path in my.cnf back to what it was, and remove the new ibdata files InnoDB created in this failed attempt. InnoDB only wrote those files full of zeros, but did not yet use them in any way. But be careful: do not remove old data files which contain your precious data!
2019-04-12T11:32:19.046718Z 1 [ERROR] [MY-012929] [InnoDB] InnoDB Database creation was aborted with error Cannot open a file. You may need to delete the ibdata1 file before trying to start up again.
2019-04-12T11:32:19.648732Z 0 [ERROR] [MY-010020] [Server] Data Dictionary initialization failed.
2019-04-12T11:32:19.648754Z 0 [ERROR] [MY-013236] [Server] Newly created data directory /var/lib/mysql/ is unusable. You can safely remove it.
2019-04-12T11:32:19.649451Z 0 [ERROR] [MY-010119] [Server] Aborting
2019-04-12T11:32:19.650326Z 0 [System] [MY-010910] [Server] /usr/sbin/mysqld: Shutdown complete (mysqld 8.0.15)  MySQL Community Server - GPL.
```


