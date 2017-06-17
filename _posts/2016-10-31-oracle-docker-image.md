---
layout: post
title: Docker image for locally testing Oracle
date: '2016-10-31T11:43:00.011+02:00'
author: enrico_spinielli
comments: true
tags:
- docker
- oracle
- OSX
---

# Docker for Oracle

This post describes my journey to building and using a docker image for Oracle.

It all started as usual searching the web for hints and many results led to
the [Oracle docker Github repository](https://github.com/oracle/docker-images).

This repository under `OracleDatabase` provides (almost) all the informations needed.

My target machine: MacBook Pro (13-inch, Mid 2009), OS X Yosemite 10.10.5.

## Which Oracle RDBMS?

I decided to use `Oracle XE 11.2.0.2` because my use case just needed an Oracle DB,
so no need to go for bigger and fancier versions.

## Steps

So without further ado here is what I tried and what did not work and which solutions
I found:

* `:thumbsup:` clone the relevant github repo:
  
  ```shell
  $ git clone https://github.com/oracle/docker-images oracle-docker-images
  $ cd oracle-docker-images/OracleDatabase/dockerfiles
  ```

* `:disappointed:` download the relevant file from Oracle, in my case, `oracle-xe-11.2.0-1.0.x86_64.rpm.zip`.
  Put it in the `11.2.0.2` directory (*DO NOT UNZIP*).
  (Of course Oracle makes this step a pain in the neck, i.e. no simple `curl` or
  any other possibility to just download it...yes with the implicit acknowledgement that you
  agree with their terms...)
  
* `:thumbsup:` run you docker daemon. I am on an old MBP, so no native docker...
  Remember to note down the docker machine IP, `docker` startup spits something like:
  ```docker is configured to use the default machine with IP 192.168.99.100```
  
* I discovered this later on once I had built a docker image...for non native docker you need
  to increase the swap memory of your VM:
  
  ```shell
  $ docker-machine stop default
  $ VBoxManage modifyvm default --memory 8192
  $ docker-machine start default
  ```

* `:thumbsdown:` as per README, execute the build script (make sure your docker daemon is running):

  ```shell
  $ ./buildDockerImage.sh -v 11.2.0.2 -x
  Checking if required packages are present and valid...
  usage: md5sum [file...]
  MD5 for required packages to build this image did not match!
  Make sure to download missing files in folder 11.2.0.2.
  ```
  
* `:thumbsup:` So...maybe that is why the `-i` flag has been added...
  
  ```shell
  $ ./buildDockerImage.sh -v 11.2.0.2 -x -i
    Ignored MD5 checksum.
    =====================
    Building image 'oracle/database:11.2.0.2-xe' ...
    Sending build context to Docker daemon 315.9 MB
    Step 1 : FROM oraclelinux:latest
    ...
    Step 2 : MAINTAINER Gerald Venzl <gerald.venzl@oracle.com>
    ...
    Step 3 : ...
    ...
    Step 4 : ENV PATH $ORACLE_HOME/bin:$PATH
    ...
    Step 5 : COPY $INSTALL_FILE_1 $CONFIG_RSP $RUN_FILE $PWD_FILE $INSTALL_DIR/
    ...
    Step 6 : RUN yum -y install ...
    ...
    Step 7 : VOLUME $ORACLE_BASE/oradata
    ...
    Step 8 : EXPOSE 1521 8080
    ...
    Step 9 : CMD $ORACLE_BASE/$RUN_FILE
    ...
    Oracle Database Docker Image for 'xe' version 11.2.0.2 is ready to be extended:
    --> oracle/database:11.2.0.2-xe
    
    Build completed in 301 seconds.
  ```

* Create a place where the DB will be persisted between runs:
  
  ```shell
  $ mkdir -p ~/var/lib/docker/oracle/$USER
  ```

* Run it (and note down the automatically generated password):
  
  ```shell
  $ docker run --shm-size=1g \
      -p 1521:1521 -p 8080:8080 \
      -v $HOME/var/lib/docker/db/$USER:/u01/app/oracle/oradata \
      oracle/database:11.2.0.2-xe
  ```

* Change the default password:
  
  ```shell
  $ docker ps
  CONTAINER ID        IMAGE                         ...
  22088d2ed7a3        oracle/database:11.2.0.2-xe   ...

  $ docker exec 22088d2ed7a3 /u01/app/oracle/setPassword.sh cucu
  ```

* and connect to it (the IP `192.168.99.100` is the one from above;
  `sql` is new Oracle's command line interface,
  see [sqlcl](http://www.oracle.com/technetwork/developer-tools/sqlcl/overview/index.html)):
  
  ```shell
  $ ~/tools/sqlcl/bin/sql system/cucu@//192.168.99.100:1521/XE
  ```
