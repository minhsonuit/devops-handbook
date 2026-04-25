# Messaging Patterns

> Ngay bat dau: ___

## Pub/Sub (Publish-Subscribe)

```
Publisher → Topic/Exchange → Consumer 1
                           → Consumer 2
                           → Consumer 3

Use case: Event notification (OrderCreated → Email + Inventory + Analytics)
Moi consumer nhan DU message.
```

## Work Queue (Competing Consumers)

```
Producer → Queue → Consumer 1 (xu ly message A)
                 → Consumer 2 (xu ly message B)
                 → Consumer 3 (xu ly message C)

Use case: Task distribution (image resize, email send)
Moi message chi 1 consumer xu ly.
Scale = them consumers.
```

## Fan-out

```
Event → Exchange/Topic → Queue 1 (Email service)
                       → Queue 2 (SMS service)
                       → Queue 3 (Push notification)

Moi service co queue rieng, xu ly doc lap.
```

## Dead Letter Queue (DLQ)

Message khong xu ly duoc → chuyen vao DLQ de xem xet sau.

```
Main Queue → Consumer → thanh cong → ACK
                      → that bai (3 retries) → Dead Letter Queue
                                                      ↓
                                              Admin xem + xu ly
```

```csharp
// MassTransit: tu dong DLQ
cfg.ReceiveEndpoint("order-events", e =>
{
    e.UseMessageRetry(r => r.Interval(3, TimeSpan.FromSeconds(5)));
    // Sau 3 retries → tu dong chuyen vao order-events_error queue
    e.ConfigureConsumer<OrderConsumer>(ctx);
});
```

## Retry Patterns

| Pattern | Mo ta | Config |
|---------|-------|--------|
| Immediate | Retry ngay | retries: 3 |
| Fixed delay | Doi X giay | interval: 5s, retries: 3 |
| Exponential backoff | Doi tang dan | 1s, 2s, 4s, 8s... |
| Circuit breaker | Dung retry khi loi nhieu | threshold: 5 failures |

```csharp
// Polly (NuGet: Microsoft.Extensions.Http.Polly)
builder.Services.AddHttpClient("external-api")
    .AddTransientHttpErrorPolicy(p => p
        .WaitAndRetryAsync(3, retryAttempt =>
            TimeSpan.FromSeconds(Math.Pow(2, retryAttempt))));
```

## Idempotency

Message co the duoc gui 2 lan (at-least-once delivery) → consumer phai xu ly duoc trung lap.

```csharp
public async Task Consume(ConsumeContext<OrderCreated> context)
{
    var messageId = context.MessageId;

    // Kiem tra da xu ly chua
    if (await _cache.ExistsAsync($"processed:{messageId}"))
        return;  // Da xu ly → bo qua

    await ProcessOrder(context.Message);

    // Danh dau da xu ly
    await _cache.SetAsync($"processed:{messageId}", "1", TimeSpan.FromHours(24));
}
```

## Kafka vs RabbitMQ

| | Kafka | RabbitMQ |
|---|-------|----------|
| Model | Log-based (append) | Queue-based (delete after consume) |
| Retention | Giu message theo thoi gian | Xoa sau khi ACK |
| Throughput | Rat cao (millions/s) | Cao (100k/s) |
| Replay | Co (re-read messages) | Khong |
| Use case | Event streaming, logs | Task queue, RPC |
| Ordering | Per partition | Per queue |
