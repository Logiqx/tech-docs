## Migration

This document will be focused on Docker Desktop for Windows. These steps were used to migrate my existing Docker containers and volumes to my new laptop.

Unfortunately my old laptop had become unable to start Docker Desktop so I needed to find a way to migrate the volumes without using Docker itself to export the volume data.



### Volumes

Migrating Docker volume data without using Docker on the original machine was consisted of 4 steps:



#### 1. Export the WSL distro

Docker Desktop volumes are stored within the "docker-desktop-data" distro in WSL.

Exported the whole docker-desktop-data distro into a tarfile:

```
wsl --export docker-desktop-data docker-desktop-data.tar
```



#### 2. Extract Docker Volumes

Docker volume files are stored in "./version-pack-data/community/docker/volumes" within the TAR.

Extract the files into a local folder, using root to retain the UID and GID details:

```sh
sudo tar xvf docker-desktop-data.tar ./version-pack-data/community/docker/volumes 2>&1 | grep -v LIBARCHIVE
```



#### 3. Create Compressed TAR Files

A list of volumes can be determined as follows:

```sh
sudo ls ./version-pack-data/community/docker/volumes/
```

Create a single TAR for each volume as follows:

```sh
sudo tar -czf caesar_db.tar.gz -C ./version-pack-data/community/docker/volumes/caesar_db/_data .
sudo tar -czf wca_mariadb.tar.gz -C ./version-pack-data/community/docker/volumes/wca_mariadb/_data .
```



#### 4. Restore Docker Volumes

TODO

```sh
# BLAH, BLAH, BLAH
```

