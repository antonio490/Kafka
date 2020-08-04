# Apache Kafka

### Prerequisites

<code>

    $ sudo apt update
    $ sudo apt isntall default-jdk
</code>

- Set JAVA_HOME environment variable
<code>

    $ export JAVA_HOME="/usr/lib/jvm/{default-jdk}"
    $ sudo nano /etc/environment

    JAVA_HOME="/usr/lib/jvm/{default-jdk}"
</code>

### Download Apache Kafka

<code>

    $ wget http://www-us.apache.org/dist/kafka/1.0.1/kafka_2.12-1.0.1.tgz
    
    $ tar xzf kafka_2.12-1.0.1.tgz

    $ mv kafka_2.12-1.0.1 /usr/local/kafka
</code>

### Start Kafka server

<code>

    $ cd /usr/local/kafka

    $ bin/zookeeper-server-start.sh config/zookeeper.properties

    $ bin/kafka-server-start.sh config/server.properties
</code>

### Create a Topic in Kafka

Let's create a topic named "testTopic" with a single partition and only one replica:
<code>

    $ bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic testTopic

    $ bin/kafka-topics.sh --list --zookeeper localhost:2181
</code>

### Send Messages to Kafka

<code>

    $ bin/kafka-console-producer.sh --broker-list localhost:9092 --topic testTopic
</code>

<!-- image with list of messages sent -->
![Image 1](https://github.com/antonio490/producerConsumer/blob/master/images/consumer.png)

### Using Kafka consumer

<code>

    $ bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic testTopic --from-beginning 
</code>

![Image 2](https://github.com/antonio490/producerConsumer/blob/master/images/producer.png)