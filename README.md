# postgres-streaming-replication

This is a sample for  master/slave [Postgres](https://www.postgresql.org/) database replication using [Streaming replication](https://www.postgresql.org/docs/9.6/static/warm-standby.html#STREAMING-REPLICATION)

> This is a simple project using docker swarm mode.

# Table of contents
1. [Configuration](#configuration)
    1. [Play With Docker](#playwithdocker)
    2. [Create swarm cluster](#swarmcluster)
    3. [Get the docker compose file](#dockercompose)
    4. [Create the services](#services)
    5. [Connect  to master database](#masterdb)
    6. [Run scripts in master database ](#script-master)
    7. [Connect to slave database](#slavedb)
    8. [Verify scripts in slave database](#script-slave)
2. [Todos](#todos)
3. [License](#license)


### Configuration<a name="configuration"></a>

1) Open a web browser and go to [Play With Docker](play-with-docker.com) tool:<a name="playwithdocker"></a>
```sh
https://play-with-docker.com
```

<p align="center">
<img src="https://github.com/guedim/postgres-streaming-replication/blob/master/resources/images/playwithdocker.png" height="250" width="1000" >
</p>


2) Create one instache, however to avoid performance issues we recommend you to create a swarm cluster using the [PWD](play-with-docker.com) templates  (3 Managers and 2 Workers  or 5 Managers and no workers).<a name="swarmcluster"></a>

<p align="center">
<img src="https://github.com/guedim/postgres-streaming-replication/blob/master/resources/images/3manager2workers.png" height="260" width="310" >
</p>
   


3) Download the [docker-compose](https://docs.docker.com/compose/) file in the new instance created in the above step:<a name="dockercompose"></a>
```sh
wget https://raw.githubusercontent.com/guedim/postgres-streaming-replication/master/docker-compose.yml
```

4) Start the services in a [Swarm Mode](https://docs.docker.com/engine/swarm/):<a name="services"></a>
```sh
docker stack deploy --compose-file docker-compose.yml postgres-streaming-replication
```
<p align="center">
<img src="https://github.com/guedim/postgres-streaming-replication/blob/master/resources/images/docker-stack-deploy.png" height="400" width="600" >
</p>


5) Go to [PgAdmin](https://www.pgadmin.org/) portal (clic in **5050** port) and register the master database.<a name="masterdb"></a>

> Open the PgAdmin for master database with the next credentials: 
>  - **user:** masterdatabase
>  - **password:** 12345678

<p align="center">
<img src="https://github.com/guedim/postgres-streaming-replication/blob/master/resources/images/masterdb.png" height="250" width="300" >
</p>

> Register the master database with:
> - **user:** postgres
> - **password:** postgres
> - **password:** *5432*

<p align="center">
<img src="https://github.com/guedim/postgres-streaming-replication/blob/master/resources/images/masterconnection.png"height="380" width="250">
</p>

6) Create a table and insert some data in the master database.<a name="script-master"></a>
```sql
-- Create a sample table
CREATE TABLE replica_test (hakase varchar(100));
-- Insert sample data
INSERT INTO replica_test VALUES ('First data in master');
INSERT INTO replica_test VALUES ('Second data from master database');
INSERT INTO replica_test VALUES ('3rd and final data from master database');
```

<p align="center">
<img src="https://github.com/guedim/postgres-streaming-replication/blob/master/resources/images/script-master.png" height="240" width="650">
</p>


7) Go to [PgAdmin](https://www.pgadmin.org/) portal (clic in **5060** port) and register the slave database.<a name="slavedb"></a>

> Open the PgAdmin for slave database with the next credentials: 
>  - **user:** slavedatabase
>  - **password:** 12345678

<p align="center">
<img src="https://github.com/guedim/postgres-streaming-replication/blob/master/resources/images/slavedb.png" height="250" width="300" >
</p>


> Register the slave database with:
> - **user:** postgres
> - **password:** postgres
> - **password:** *5433*

<p align="center">
<img src="https://github.com/guedim/postgres-streaming-replication/blob/master/resources/images/slaveconnection.png" height="380" width="250" >
</p>


8) Finally, verify the data in the slave database:<a name="script-slave"></a>
```sql
-- Verify the data in the slave database
select * from replica_test;
```
<p align="center">
<img src="https://github.com/guedim/postgres-streaming-replication/blob/master/resources/images/script-slave.png" height="240" width="650" >
</p>


### Todos<a name="todos"></a>

 - How to use a cluster using streaming and logical replication

### License<a name="license"></a>
----
MIT
