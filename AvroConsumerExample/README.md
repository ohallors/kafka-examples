This example reads Avro records containing click information, as produced by AvroProducerExample.
We enrich them with session information and publish both to stdout and to another Kafka topic.

Consuming data from one topic, processing a bit and producing to another topic is a very common usage pattern.

To manage session state, we need an in-memory table with last connection time for each client.
Since this is a simple example, we don't worry about persisting or purging this table.
Maybe this will arrive later :)

To build this producer:

    $ cd AvroProducerExample
    $ mvn clean package
    
Quickstart
-----------

Before running the examples, make sure that Zookeeper, Kafka and Schema Registry are
running. In what follows, we assume that Zookeeper, Kafka and Schema Registry are
started with the default settings.

    # Start Zookeeper
    $ bin/zookeeper-server-start config/zookeeper.properties

    # Start Kafka
    $ bin/kafka-server-start config/server.properties

    # Start Schema Registry
    $ bin/schema-registry-start config/schema-registry.properties
    
If you don't already have a schema registry, you will need to install it.
Either from packages: http://www.confluent.io/developer
Or from source: https://github.com/confluentinc/schema-registry

Also, this example works with data generated by AvroClicksProducer, so start by running that project first.
    
Then create a topic called sessions:

    # Create page_visits topic
    $ bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 \
      --partitions 1 --topic sessions
      

Then run the consumer to enrich the clicks with session information:

    $ java -cp target/uber-ClickSessionizer-1.0-SNAPSHOT.jar com.shapira.examples.consumer.avroclicks.AvroClicksSessionizer http://localhost:8081
    
You can validate the result by using the avro console consumer (part of the schema repository):

    $ bin/kafka-avro-console-consumer --zookeeper localhost:2181 --topic sessions --from-beginning