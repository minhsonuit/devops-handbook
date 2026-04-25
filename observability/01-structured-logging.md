# Structured Logging

> Ngay bat dau: ___

## Tai sao structured logging

```
// Unstructured (kho parse, kho query)
"2026-04-25 07:00:00 User John created order #1234 for $50.00"

// Structured (de parse, de query, de filter)
{
  "timestamp": "2026-04-25T07:00:00Z",
  "level": "Information",
  "message": "Order created",
  "userId": "John",
  "orderId": 1234,
  "amount": 50.00,
  "correlationId": "abc-123"
}
```

## Serilog cho .NET

```csharp
// NuGet: Serilog.AspNetCore, Serilog.Sinks.Console, Serilog.Sinks.Seq
builder.Host.UseSerilog((ctx, config) => config
    .MinimumLevel.Information()
    .MinimumLevel.Override("Microsoft", LogEventLevel.Warning)
    .WriteTo.Console(new JsonFormatter())
    .WriteTo.Seq("http://seq:5341")          // Optional: Seq server
    .Enrich.FromLogContext()
    .Enrich.WithMachineName()
    .Enrich.WithEnvironmentName()
    .Enrich.WithProperty("Application", "MyAPI"));
```

### Log voi context

```csharp
// SAI — string concatenation
_logger.LogInformation($"Order {orderId} created by user {userId}");

// DUNG — structured
_logger.LogInformation("Order {OrderId} created by user {UserId}", orderId, userId);
// Serilog se luu OrderId va UserId nhu fields rieng → de query
```

## Correlation ID

Theo doi 1 request xuyen suot nhieu services.

```csharp
// Middleware: tao hoac forward Correlation ID
public class CorrelationIdMiddleware
{
    public async Task InvokeAsync(HttpContext context, RequestDelegate next)
    {
        var correlationId = context.Request.Headers["X-Correlation-ID"].FirstOrDefault()
                           ?? Guid.NewGuid().ToString();

        context.Response.Headers["X-Correlation-ID"] = correlationId;

        using (LogContext.PushProperty("CorrelationId", correlationId))
        {
            await next(context);
        }
    }
}
```

```csharp
// Khi goi service khac, forward Correlation ID
httpClient.DefaultRequestHeaders.Add("X-Correlation-ID", correlationId);
```

## Log levels

| Level | Khi nao dung | Vi du |
|-------|-------------|-------|
| Verbose/Trace | Debug chi tiet | SQL queries, request body |
| Debug | Development | Variable values, flow steps |
| Information | Normal operations | "Order created", "User logged in" |
| Warning | Co the co van de | "Cache miss", "Retry attempt 2" |
| Error | Co loi nhung app van chay | Exception trong 1 request |
| Fatal/Critical | App khong chay duoc | DB connection failed, out of memory |

**Production nen dat:** `Information` tro len. `Debug/Trace` chi khi can.

## Tim log trong Elasticsearch/Kibana

```json
// Query logs co loi theo correlation ID
{
  "query": {
    "bool": {
      "must": [
        { "match": { "fields.CorrelationId": "abc-123" } },
        { "match": { "level": "Error" } }
      ]
    }
  },
  "sort": [{ "@timestamp": "asc" }]
}
```

## Output format cho Docker

```csharp
// JSON format — tot cho log aggregation
.WriteTo.Console(new JsonFormatter())

// Compact format — doc duoc va parse duoc
.WriteTo.Console(new RenderedCompactJsonFormatter())
```

Khi log ra JSON, `docker logs` + `jq` rat manh:

```bash
docker logs api 2>&1 | jq 'select(.level == "Error")'
docker logs api 2>&1 | jq 'select(.fields.CorrelationId == "abc-123")'
docker logs api 2>&1 | jq 'select(.fields.OrderId == 1234)'
```
