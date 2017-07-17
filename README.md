# Oracle DB 12c EE in Docker

With the stuff in this repo you can install the Oracle DB with Docker.

## Prerequisites

You need to have [Docker](https://www.docker.com/) installed on your machine.

[Download](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html) 
and save the Oracle Database 12c EE installation files `linuxamd64_12102_database_1of2.zip` 
and `linuxamd64_12102_database_2of2.zip` in directory `step1`:

## Setup

Execute the following commands in a shell:

    $> cd docker-12c-ee-dbca
    $> docker build -t oracle-12c:step1 step1
    $> docker run --shm-size=4g -ti --name step1 oracle-12c:step1 /bin/bash
    $> /tmp/install/install

Wait for Successfully Setup Software. Takes several minutes.

    $> [press enter]
    $> exit
    $> docker commit step1 oracle-12c:installed
    $> docker build -t oracle-12c:step2 step2
    $> docker run --shm-size=4g -ti --name step2 oracle-12c:step2 /bin/bash
    $> /tmp/create
    $> su - oracle 
    $> cd /tmp/

Use your custom name for your pluggable database [YOUR_PDB_NAME]:

    $> dbca -silent \
       -createDatabase \
       -templateName General_Purpose.dbc \
       -gdbname ORCL \
       -sid ORCL \
       -responseFile ./db_install.rsp  \
       -characterSet AL32UTF8  \
       -sysPassword admin  \
       -systemPassword admin  \
       -createAsContainerDatabase true  \
       -numberOfPDBs 1 \
       -pdbName [YOUR_PDB_NAME] \
       -pdbAdminPassword admin \
       -databaseType MULTIPURPOSE  \
       -automaticMemoryManagement false \
       -storageType FS \
       -ignorePreReqs

Wait again several minutes.

    $> exit
    $> exit
    $> docker commit step2 oracle-12c:created
    $> docker build -t oracle-12c step3

Image has been created, congrats!

Now start the database image for the first time:

    $> docker run --shm-size=4g -p 1521:1521 -ti --name fresh_db oracle-12c:latest /bin/bash

or any subsequent start:

    $> docker start fresh_db
    $> docker exec -it fresh_db bash

To run Oracle in the container:

    $> /tmp/start

## Some Useful  Docker Comands

* To open a console on the docker image: `docker exec -it fresh_db bash`
* List all images you have created. You can create a new image upon an existing one, if you want to make some changes: `docker images` 
* List all running containers: `docker ps -a` 
* Start a container: `docker start <container ID or tag>`
