docker exec -it broker kafka-topics --zookeeper zookeeper:2181 --create --topic usuarios --partitions 1 --replication-factor 1

curl -X POST http://localhost:8083/connectors -H "Content-Type: application/json" -d '{
"name": "usuarios-elasticsearch-connector",
"config": {
"connector.class": "io.confluent.connect.elasticsearch.ElasticsearchSinkConnector",
"connection.url": "http://elasticsearch:9200",
"tasks.max": 1,
"topics": "usuarios",
"batch.size": 2000,
"max.buffered.records": 20000,
"topic.index.map": "usuarios:usuarios",
"type.name": "main",
"value.converter": "org.apache.kafka.connect.json.JsonConverter",
"value.converter.schemas.enable": "false",
"schema.ignore": "true",
"linger.ms": 1,
"key.ignore": true,
"schema.ignore": true,
"flush.timeout.ms" : 100000,
"max.buffered.records" : 2000,
"max.retries" : 5000,
"retry.backoff.ms" : 1000
}
}'

kafkacat -P -b localhost:9092 -t usuarios

{"name": "Ales", "age": 29}
{"name": "Claudia", "age": 31}
{"name": "Elsa", "age": 19}
{"name": "Ramón", "age": 19}
{"name": "Mónica", "age": 19}
{"name": "Felipe", "age": 19}
{"name": "Alfonso", "age": 19}
{"name": "Juan", "age": 19}
{"name": "Claudia", "age": 31}
{"name": "Pau", "age": 31}
{"name": "Jose", "age": 31}
{"name": "Juan", "age": 31}

curl localhost:9200/usuarios/_search | jq

