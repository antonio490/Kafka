
# Kafka Connect

### Source

Start up kafka cluster with our docker-compose.

     $ docker-compose up kafka-cluster

We start a hosted tools, mapped on our code

     docker run --rm -it -v "$(pwd)":/tutorial --net=host landoop/fast-data-dev:cp3.3.0 bash

#### Standalone

Create a Topic demo-1-standalone

    $ kafka-topics --create --topic demo-1-standalone --partitions 3 --replication-factor 1 --zookeeper 127.0.0.1:2181

<code>
     
     name=file-stream-demo-standalone
     connector.class=org.apache.kafka.connect.file.FileStreamSourceConnector
     tasks.max=1
     file=demo-file.txt
     topic=demo-1-standalone

</code>

We use the fileStreamSourceConnector and the connect worker properties (only for standalone purposes).
     
     $ connect-standalone worker.properties file-stream-demo-standalone.properties

<code>

     bootstrap.servers=127.0.0.1:9092
     key.converter=org.apache.kafka.connect.json.JsonConverter
     key.converter.schemas.enable=false
     value.converter=org.apache.kafka.connect.json.JsonConverter
     value.converter.schemas.enable=false
     internal.key.converter=org.apache.kafka.connect.json.JsonConverter
     internal.key.converter.schemas.enable=false
     internal.value.converter=org.apache.kafka.connect.json.JsonConverter
     internal.value.converter.schemas.enable=false
     rest.port=8086
     rest.host.name=127.0.0.1
     offset.storage.file.filename=standalone.offsets
     offset.flush.interval.ms=10000

</code>

#### Distributed

Create a topic demo-2-distributed

     $ kafka-topics --create --topic demo-2-distributed --partitions 3 --replication-factor 1 --zookeeper 127.0.0.1:2181

We can read the data with the kafka consumer service

     $ kafka-console-consumer --topic demo-2-distributed --from-beginning --bootstrap-server 127.0.0.1:9092

<code>

     name=file-stream-demo-distributed
     connector.class=org.apache.kafka.connect.file.FileStreamSourceConnector
     tasks.max=1
     file=demo-file.txt
     topic=demo-2-distributed
     key.converter=org.apache.kafka.connect.json.JsonConverter
     value.converter=org.apache.kafka.connect.json.JsonConverter
     
</code>

### Sink

#### ElasticSearch

Create a new sink connector type ElasticSearch confluent, add its properties and make sure the topic is enable and the twitter source connector we created before is also enable and publishing data into the demo-3-twitter topic.

      # Now we have to run the docker with alias elasticsearch to map it.
      $ docker-compose up kafka-cluster elasticsearch postgres 

<code>

     name=sink-elastic-twitter-distributed
     connector.class=io.confluent.connect.elasticsearch.ElasticsearchSinkConnector
     tasks.max=2
     topics=demo-3-twitter
     key.converter=org.apache.kafka.connect.json.JsonConverter
     value.converter=org.apache.kafka.connect.json.JsonConverter
     connection.url=http://elasticsearch:9200
     type.name=kafka-connect
     key.ignore=true
     topic.index.map="demo-3-twitter:index1"
     topic.key.ignore=true
     topic.schema.ignore=true

</code>

#### PostgreSQL

In order to sink twitter posts into a postgress database we just need to repeat the same cycle but this time creating a jdbc sink connector with the appropiate properties. 

<code>

     name=sink-postgres-twitter-distributed
     connector.class=io.confluent.connect.jdbc.JdbcSinkConnector
     tasks.max=1
     topics=demo-3-twitter
     key.converter=org.apache.kafka.connect.json.JsonConverter
     value.converter=org.apache.kafka.connect.json.JsonConverter
     connection.url=jdbc:postgresql://postgres:5432/postgres
     connection.user=postgres
     connection.password=postgres
     insert.mode=upsert
     pk.mode=kafka
     pk.fields=__connect_topic,__connect_partition,__connect_offset
     fields.whitelist=id,created_at,text,lang,is_retweet
     auto.create=true
     auto.evolve=true

</code>