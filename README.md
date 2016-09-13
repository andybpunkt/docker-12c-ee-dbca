# docker-12c-ee-dbca

You need to have Docker installed on your machine. https://www.docker.com/

For docker images go to: https://hub.docker.com/r/andybpunkt/oracle-12/

Pull request with Docker: docker pull andybpunkt/oracle-12:fresh_db

Save the Oracle Database 12c EE installation files 'linuxamd64_12102_database_1of2.zip' 
and 'linuxamd64_12102_database_2of2.zip' in directory Step1:

http://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html

Execute the following commands in a console (e.g. ConEmu is easy to use):

- cd /directory of checkedout repository

- docker build -t oracle-12c:step1 step1

- docker run --shm-size=4g -ti --name step1 oracle-12c:step1 /bin/bash

- /tmp/install/install

Wait for Successfully Setup Software!Takes several minutes.

- press enter

- exit

- docker commit step1 oracle-12c:installed

- docker build -t oracle-12c:step2 step2

- docker run --shm-size=4g -ti --name step2 oracle-12c:step2 /bin/bash

- /tmp/create

- su - oracle 

- cd /tmp/

- dbca -silent -createDatabase -templateName General_Purpose.dbc  -gdbname ORCL -sid ORCL -responseFile ./db_install.rsp  -characterSet AL32UTF8  -sysPassword admin  -systemPassword admin  -createAsContainerDatabase true  -numberOfPDBs 1  -pdbName pdb_orcldb  -pdbAdminPassword admin  -databaseType MULTIPURPOSE  -automaticMemoryManagement false  -storageType FS  -ignorePreReqs

Wait again several minutes.

- 2x exit

- docker commit step2 oracle-12c:created

- docker build -t oracle-12c step3
---------------------------------------------------------------------------
Image has been created! Congrats

Now start the database:

- docker run --shm-size=4g -p 1521:1521 -ti --name fresh_db oracle-12c:latest /bin/bash

- /tmp/start


To open a console on the docker image: docker exec -it fresh_db bash

'docker images' returns all images you created. You can create a new image upon an existing one, if you want to make some changes. 

'docker ps -a' returns all images containers. 'docker start "container ID orTag"' starts a container.
