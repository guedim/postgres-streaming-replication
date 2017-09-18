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

![Play With Docker](https://github.com/guedim/postgres-streaming-replication/blob/master/resources/images/playwithdocker.png "Play With Docker")


2) Create one instache, however to avoid performance issues we recommend you to create a swarm cluster using the [PWD](play-with-docker.com) templates  (3 Managers and 2 Workers  or 5 Managers and no workers).<a name="swarmcluster"></a>

![Play With Docker Template](https://github.com/guedim/postgres-streaming-replication/blob/master/resources/images/3manager2workers.png "Play With Docker - Template")


3) Download the [docker-compose](https://docs.docker.com/compose/) file in the new instance created in the above step:<a name="dockercompose"></a>
```sh
wget https://raw.githubusercontent.com/guedim/postgres-streaming-replication/master/docker-compose.yml
```

4) Start the services in a [Swarm Mode](https://docs.docker.com/engine/swarm/):<a name="services"></a>
```sh
docker stack deploy --compose-file docker-compose.yml postgres-kafka-es
```
![Docker services](https://github.com/guedim/postgres-streaming-replication/blob/master/resources/images/docker-stack-deploy.png "Docker Services")



5) Go to [Landoop](http://www.landoop.com/) portal (clic in 3030 port), for example:<a name="landoop"></a>

```sh
http://pwd10-0-7-3-3030.host2.labs.play-with-docker.com/
```

![Landoop](https://github.com/guedim/postgres-kafka-elastic/blob/master/resources/images/landoop.png "Landoop portal")

6) In the [Landoop](http://www.landoop.com/) portal, create and set up the Postgres Kafka  using the [JDBC](http://docs.confluent.io/current/connect/connect-jdbc/docs/index.html) connector:<a name="connector"></a>
```sh
name=source-postgres
connector.class=io.confluent.connect.jdbc.JdbcSourceConnector
tasks.max=1
connection.url=jdbc:postgresql://pwd10-0-7-3-5432.host2.labs.play-with-docker.com:5432/postgres?user=postgres&password=postgres
topic.prefix=postgres_
mode=timestamp+incrementing
incrementing.column.name=id
timestamp.column.name=updated_at
value.converter=org.apache.kafka.connect.json.JsonConverter
key.converter=org.apache.kafka.connect.json.JsonConverter
```
> Dont forget to change the **connection.url parameter** using the host with the 5432 port

![Landoop - Postgres](https://github.com/guedim/postgres-kafka-elastic/blob/master/resources/images/landoop-postgres.png "Landoop - Postgres")


7) In the [Landoop](http://www.landoop.com/) portal, create and set up the [kafka elastic Sink Confluent](http://docs.confluent.io/current/connect/connect-elasticsearch/docs/elasticsearch_connector.html):<a name="sink"></a>
```sh
name=ElasticsearchSinkConnector
connector.class=io.confluent.connect.elasticsearch.ElasticsearchSinkConnector
topics=postgres_users
tasks.max=1
connection.url=http://pwd10-0-7-3-9200.host2.labs.play-with-docker.com:9200
type.name=kafka-connect
topic.key.ignore=true
key.ignore=true
value.converter=org.apache.kafka.connect.json.JsonConverter
key.converter=org.apache.kafka.connect.json.JsonConverter
topic.schema.ignore=true
```
> Dont forget to change the **connection.url** parameter using the host with the 9200 port

![Landoop - ElasticSearch](https://github.com/guedim/postgres-kafka-elastic/blob/master/resources/images/landoop-es.png "Landoop - ElasticSearch")


8) With a Postgres client, connect to  the database using the next credentials:<a name="postgres"></a>
```sh
- User: postgres
- password: postgres
- database: postgres
```
> You can use the next commands to connect in the PWD terminal:
> docker service ps postgres-kafka-es
> docker exec -it POSTGRES_CONTAINER_NAME /bin/bash
> psql -U postgres -d postgres

And create the table **users** and insert some sample data:


```sql
-- Create the table users
CREATE TABLE users
(
    id          SERIAL NOT NULL,
    name        VARCHAR(100) NOT NULL,
    age         INTEGER,
    updated_at  TIMESTAMP NOT NULL DEFAULT NOW(),
    PRIMARY KEY(id)
);

-- Insert some data:
INSERT INTO users (name, age) VALUES ('john', 26);
INSERT INTO users (name, age) VALUES ('jane', 24);
INSERT INTO users (name, age) VALUES ('julia', 25);
INSERT INTO users (name, age) VALUES ('jamie', 22);
INSERT INTO users (name, age) VALUES ('jenny', 27);
```

![Postgres - Insert](https://github.com/guedim/postgres-kafka-elastic/blob/master/resources/images/insrt-postgres.png "Postgres - Insert")


9) Finally, you can query the Postgres data in the kafka topic or in the ElasticSearch Tool, just click in the port 9200 and go to **_plugin/dejavu** (dont forget to use the postgres_users index):<a name="end"></a>

- In the Kafka Topic
![Kafka Postgres Topic](https://github.com/guedim/postgres-kafka-elastic/blob/master/resources/images/Topic.png "Kafka Postgres topic")

- In the **dejavu**  ElasticSearch plugin:

```sh
http://192.168.99.100:9200/_plugin/dejavu
```

![ElasticSearch - Dejavu](https://github.com/guedim/postgres-kafka-elastic/blob/master/resources/images/elastic-dejavu.png "ElasticSearch - Dejavu")



### Todos<a name="todos"></a>

 - How to create the sql data
 - Create all the data and configuration automatically

### License<a name="license"></a>
----
MIT
