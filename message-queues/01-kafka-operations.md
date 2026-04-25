# Kafka Operations

> Ngay bat dau: ___

## Kafka trong Docker

```yaml
services:
  kafka:
    image: confluentinc/cp-kafka:7.6.0
    ports:
      - "9092:9092"
    environment:
      KAFKA_NODE_ID: 1
      KAFKA_PROCESS_ROLES: broker,controller
      KAFKA_CONTROLLER_QUORUM_VOTERS: 1@kafka:9093
      KAFKA_LISTENERS: PLAINTEXT://0.0.0.0:9092,CONTROLLER://0.0.0.0:9093
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://localhost:9092
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT
      KAFKA_CONTROLLER_LISTENER_NAMES: CONTROLLER
      KAFKA_LOG_DIRS: /var/lib/kafka/data
      KAFKA_AUTO_CREATE_TOPICS_ENABLE: "false"
      KAFKA_NUM_PARTITIONS: 3
      KAFKA_DEFAULT_REPLICATION_FACTOR: 1
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
    volumes:
      - kafka_data:/var/lib/kafka/data
    restart: unless-stopped

  kafka-ui:
    image: provectuslabs/kafka-ui:latest
    ports:
      - "8080:8080"
    environment:
      KAFKA_CLUSTERS_0_NAME: local
      KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS: kafka:9092

volumes:
  kafka_data:
```

## CLI co ban

```bash
KAFKA="docker exec kafka"

# Topics
$KAFKA kafka-topics --bootstrap-server localhost:9092 --list
$KAFKA kafka-topics --bootstrap-server localhost:9092 --create --topic orders --partitions 3 --replication-factor 1
$KAFKA kafka-topics --bootstrap-server localhost:9092 --describe --topic orders
$KAFKA kafka-topics --bootstrap-server localhost:9092 --delete --topic orders

# Produce
$KAFKA kafka-console-producer --bootstrap-server localhost:9092 --topic orders
# Nhap message, Enter de gui, Ctrl+C de thoat

# Consume
$KAFKA kafka-console-consumer --bootstrap-server localhost:9092 --topic orders --from-beginning
$KAFKA kafka-console-consumer --bootstrap-server localhost:9092 --topic orders --group my-group

# Consumer groups
$KAFKA kafka-consumer-groups --bootstrap-server localhost:9092 --list
$KAFKA kafka-consumer-groups --bootstrap-server localhost:9092 --describe --group my-group
$KAFKA kafka-consumer-groups --bootstrap-server localhost:9092 --group my-group --reset-offsets --to-earliest --topic orders --execute
```

## Monitoring

### Consumer lag (quan trong nhat)

```bash
# Xem lag
$KAFKA kafka-consumer-groups --bootstrap-server localhost:9092 --describe --group my-group
# TOPIC    PARTITION  CURRENT-OFFSET  LOG-END-OFFSET  LAG
# orders   0          1000            1050            50    ← 50 messages behind
```

### Metrics quan trong

| Metric | Y nghia | Alert khi |
|--------|---------|-----------|
| Consumer lag | Messages chua xu ly | > 1000 hoac tang lien tuc |
| Under-replicated partitions | Partition chua sync | > 0 |
| Active controller | Cluster co leader | = 0 |
| Request rate | Messages/s | Drop dot ngot |
| Disk usage | /var/lib/kafka/data | > 80% |

### Prometheus JMX Exporter

```yaml
  kafka-exporter:
    image: danielqsj/kafka-exporter:latest
    command: ["--kafka.server=kafka:9092"]
    ports:
      - "9308:9308"
```

## Troubleshooting

```bash
# Consumer khong nhan message
# 1. Kiem tra consumer group
$KAFKA kafka-consumer-groups --bootstrap-server localhost:9092 --describe --group my-group

# 2. Kiem tra topic co message khong
$KAFKA kafka-run-class kafka.tools.GetOffsetShell --broker-list localhost:9092 --topic orders

# 3. Consumer bi rebalance lien tuc?
# Tang session.timeout.ms va max.poll.interval.ms

# Disk day
# Giam retention
$KAFKA kafka-configs --bootstrap-server localhost:9092 --alter --entity-type topics --entity-name orders --add-config retention.ms=86400000
# 86400000ms = 1 ngay
```

## .NET Kafka (Confluent)

```csharp
// NuGet: Confluent.Kafka
// Producer
using var producer = new ProducerBuilder<string, string>(
    new ProducerConfig { BootstrapServers = "localhost:9092" }).Build();

await producer.ProduceAsync("orders", new Message<string, string>
{
    Key = orderId.ToString(),
    Value = JsonSerializer.Serialize(order)
});

// Consumer
using var consumer = new ConsumerBuilder<string, string>(
    new ConsumerConfig
    {
        BootstrapServers = "localhost:9092",
        GroupId = "order-processor",
        AutoOffsetReset = AutoOffsetReset.Earliest,
        EnableAutoCommit = false
    }).Build();

consumer.Subscribe("orders");
while (true)
{
    var result = consumer.Consume(cancellationToken);
    await ProcessMessage(result.Message.Value);
    consumer.Commit(result);
}
```
