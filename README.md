# Kafka-ElasticSearch connector POC

This project is a POC of Kafka-ElasticSearch connector.

## Installation

Use docker-compose to start all the containers defined in docker-compose.yml.

```bash
#Start all containers
docker-compose up

#Create a topic in Kafka named 'users'
docker exec -it broker kafka-topics --zookeeper zookeeper:2181 --create --topic users --partitions 1 --replication-factor 1

#Create a ElasticSearch connector
curl -X POST http://localhost:8083/connectors -H "Content-Type: application/json" -d '{ "name": "users-elasticsearch-connector", "config": { "connector.class": "io.confluent.connect.elasticsearch.ElasticsearchSinkConnector", "connection.url": "http://elasticsearch:9200", "tasks.max": 1, "topics": "users", "batch.size": 2000, "max.buffered.records": 20000, "topic.index.map": "users:users", "type.name": "main", "value.converter": "org.apache.kafka.connect.json.JsonConverter", "value.converter.schemas.enable": "false", "schema.ignore": "true", "linger.ms": 1, "key.ignore": true, "schema.ignore": true, "flush.timeout.ms" : 100000, "max.buffered.records" : 2000, "max.retries" : 5000, "retry.backoff.ms" : 1000 } }'
```

## Usage

Use kafkacat to send message to the topic and see how the message is forwarded to ElasticSearch automatically

```bash
#Write multiple message on the 'users' topic
kafkacat -P -b localhost:9092 -t users

{"name": "Ales", "age": 29} 
{"name": "Elsa", "age": 20} 
{"name": "Ramón", "age": 19} 
{"name": "Mónica", "age": 19} 
{"name": "Felipe", "age": 19} 

#Visualizate the content of the 'users' index on ElasticSearch
curl localhost:9200/users/_search | jq
```

