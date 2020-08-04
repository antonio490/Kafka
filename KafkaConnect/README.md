
# Kafka Connect

### Source

Start up kafka cluster with our docker-compose.

     $ docker-compose up kafka-cluster

We start a hosted tools, mapped on our code

     docker run --rm -it -v "$(pwd)":/tutorial --net=host landoop/fast-data-dev:cp3.3.0 bash

#### Standalone

Create a Topic demo-1-standalone

    $ kafka-topics --create --topic demo-1-standalone --partitions 3 --replication-factor 1 --zookeeper 127.0.0.1:2181

We use the fileStreamSourceConnector and the connect worker properties (only for standalone purposes).
     
     $ connect-standalone worker.properties file-stream-demo-standalone.properties


#### Distributed

Create a topic demo-2-distributed

     $ kafka-topics --create --topic demo-2-distributed --partitions 3 --replication-factor 1 --zookeeper 127.0.0.1:2181

We can read the data with the kafka consumer service

     $ kafka-console-consumer --topic demo-2-distributed --from-beginning --bootstrap-server 127.0.0.1:9092
### Sink

