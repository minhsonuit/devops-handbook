# RabbitMQ

> Ngay bat dau: ___

## RabbitMQ trong Docker

```yaml
services:
  rabbitmq:
    image: rabbitmq:3-management-alpine
    ports:
      - "5672:5672"       # AMQP
      - "15672:15672"     # Management UI
    environment:
      RABBITMQ_DEFAULT_USER: admin
      RABBITMQ_DEFAULT_PASS: YOUR_PASSWORD
    volumes:
      - rabbitmq_data:/var/lib/rabbitmq
    healthcheck:
      test: ["CMD", "rabbitmq-diagnostics", "check_port_connectivity"]
      interval: 15s
      timeout: 10s
      retries: 5
    restart: unless-stopped

volumes:
  rabbitmq_data:
```

Management UI: http://localhost:15672

## Concepts

```
Producer → Exchange → Binding → Queue → Consumer

Exchange types:
- direct  : route bang routing key chinh xac
- fanout  : broadcast den tat ca queues
- topic   : route bang pattern (order.* , *.created)
- headers : route bang headers
```

## CLI

```bash
RABBIT="docker exec rabbitmq"

# Queues
$RABBIT rabbitmqctl list_queues name messages consumers
$RABBIT rabbitmqctl list_queues name messages_ready messages_unacknowledged

# Exchanges
$RABBIT rabbitmqctl list_exchanges name type

# Connections
$RABBIT rabbitmqctl list_connections user peer_host state

# Xoa queue
$RABBIT rabbitmqctl delete_queue my-queue

# Purge messages
$RABBIT rabbitmqctl purge_queue my-queue
```

## Monitoring

| Metric | Alert khi |
|--------|-----------|
| Queue depth | > 10,000 messages |
| Consumer count | = 0 (khong ai xu ly) |
| Memory usage | > 80% limit |
| Messages unacked | Tang lien tuc |
| Connection count | Dat limit |

### Prometheus exporter

```yaml
  rabbitmq-exporter:
    image: kbudde/rabbitmq-exporter:latest
    environment:
      RABBIT_URL: http://rabbitmq:15672
      RABBIT_USER: admin
      RABBIT_PASSWORD: YOUR_PASSWORD
    ports:
      - "9419:9419"
```

## .NET RabbitMQ (MassTransit)

```csharp
// NuGet: MassTransit, MassTransit.RabbitMQ
builder.Services.AddMassTransit(x =>
{
    x.AddConsumer<OrderCreatedConsumer>();

    x.UsingRabbitMq((ctx, cfg) =>
    {
        cfg.Host("rabbitmq", "/", h =>
        {
            h.Username("admin");
            h.Password("YOUR_PASSWORD");
        });
        cfg.ConfigureEndpoints(ctx);
    });
});

// Consumer
public class OrderCreatedConsumer : IConsumer<OrderCreated>
{
    public async Task Consume(ConsumeContext<OrderCreated> context)
    {
        var order = context.Message;
        // Process order...
    }
}
```
