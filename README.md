# avro-references
This is source code for the [Medium article](https://medium.com/@katyagorshkova/when-avro-references-avro-f0d53bd3b58f)
# Start a docker compose

`docker compose up -d`

# Create a topic

`docker exec -ti kafka /usr/bin/kafka-topics --create \
  --bootstrap-server kafka:19092 \
  --replication-factor 1 \
  --partitions 3 \
  --topic company`

# Register header
`curl -X POST -H "Content-Type: application/json" \
--data '{"schema": "{\"type\":\"record\",\"name\":\"Header\",\"namespace\":\"org.jresearch.kafka.examples\",\"fields\":[{\"name\":\"MessageId\",\"type\":\"string\"},{\"name\":\"TraceId\",\"type\":\"string\"},{\"name\":\"Timestamp\",\"type\":{\"type\":\"long\",\"logicalType\":\"timestamp-millis\"}}]}"}' \
http://localhost:8081/subjects/org.jresearch.kafka.examples.Header/versions`

# Register Company Schema

`curl -X POST -H "Content-Type: application/json" \
--data '{
  "schema": "{\"namespace\":\"org.jresearch.kafka.examples\",\"type\":\"record\",\"name\":\"Company\",\"fields\":[{\"name\":\"header\",\"type\":\"org.jresearch.kafka.examples.Header\"},{\"name\":\"tradeNumber\",\"type\":\"int\"},{\"name\":\"registeredName\",\"type\":\"string\"},{\"name\":\"eventCreatedAt\",\"doc\":\"Timestamp of event creation in GMT time zone\",\"type\":{\"type\":\"long\",\"logicalType\":\"timestamp-millis\"}}]}",
  "references": [
    {
      "name": "org.jresearch.kafka.examples.Header",
      "subject": "org.jresearch.kafka.examples.Header",
      "version": 1
    }
  ]
}' \
http://localhost:8081/subjects/company-value/versions
`

