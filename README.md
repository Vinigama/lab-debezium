# Start de stack com Kafka e Mysql
`docker compose -f zk-single-kafka-single.yml up`

# Import de banco de dados de amostra
`docker exec -i mysqldb mysql -uroot -proot sys < mysqlsampledatabase.sql`

# Start captura de dados via Kafka Connect
`curl -i -X POST -H "Accept:application/json" -H "Content-Type:application/json" localhost:8083/connectors/ -d @./captura_connect.json`

# Listar tópicos criados (exec em kafka1)
`kafka-topics --bootstrap-server=localhost:9092 --list`

# Ver eventos
`docker run -it --rm --name watcher --network kafka-stack-docker-compose_default -e KAFKA_ADVERTISED_LISTENERS=INTERNAL://kafka1:19092,EXTERNAL://${DOCKER_HOST_IP:-127.0.0.1}:9092,DOCKER://host.docker.internal:29092 -e KAFKA_BROKER=kafka1:19092 quay.io/debezium/kafka:2.4 watch-topic -a -k classicmodelsdb.classicmodels.customers`

# Criar banco de dados (workbench):
```CREATE DATABASE replicacao```

# Start replicação de dados via Kafka Connect
`curl -i -X POST -H "Accept:application/json" -H "Content-Type:application/json" localhost:8083/connectors/ -d @./sink_connect.json`

# Converters (Kafka Connect):
- io.confluent.connect.avro.AvroConverter
- org.apache.kafka.connect.json.JsonConverter