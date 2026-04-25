# OpenTelemetry cho .NET

> Ngay bat dau: ___

## OpenTelemetry la gi

Standard mo de thu thap telemetry: **Traces, Metrics, Logs** — 1 SDK cho tat ca.

```
App (.NET) → OTel SDK → OTel Collector → Jaeger (traces)
                                        → Prometheus (metrics)
                                        → Elasticsearch (logs)
```

## Cai dat trong .NET

```csharp
// NuGet packages:
// OpenTelemetry.Extensions.Hosting
// OpenTelemetry.Instrumentation.AspNetCore
// OpenTelemetry.Instrumentation.Http
// OpenTelemetry.Instrumentation.SqlClient
// OpenTelemetry.Exporter.OpenTelemetryProtocol

builder.Services.AddOpenTelemetry()
    .ConfigureResource(resource => resource
        .AddService("MyAPI", serviceVersion: "1.0.0"))
    .WithTracing(tracing => tracing
        .AddAspNetCoreInstrumentation()
        .AddHttpClientInstrumentation()
        .AddSqlClientInstrumentation(opt => opt.SetDbStatementForText = true)
        .AddOtlpExporter(opt => opt.Endpoint = new Uri("http://otel-collector:4317")))
    .WithMetrics(metrics => metrics
        .AddAspNetCoreInstrumentation()
        .AddHttpClientInstrumentation()
        .AddRuntimeInstrumentation()
        .AddProcessInstrumentation()
        .AddOtlpExporter(opt => opt.Endpoint = new Uri("http://otel-collector:4317")));
```

## OTel Collector

```yaml
# docker-compose
services:
  otel-collector:
    image: otel/opentelemetry-collector-contrib:latest
    ports:
      - "4317:4317"     # OTLP gRPC
      - "4318:4318"     # OTLP HTTP
    volumes:
      - ./otel-config.yaml:/etc/otelcol-contrib/config.yaml
```

```yaml
# otel-config.yaml
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317

processors:
  batch:
    timeout: 5s

exporters:
  otlp/jaeger:
    endpoint: jaeger:4317
    tls:
      insecure: true
  prometheus:
    endpoint: "0.0.0.0:8889"

service:
  pipelines:
    traces:
      receivers: [otlp]
      processors: [batch]
      exporters: [otlp/jaeger]
    metrics:
      receivers: [otlp]
      processors: [batch]
      exporters: [prometheus]
```

## Custom spans

```csharp
using System.Diagnostics;

// Tao ActivitySource (tuong duong Tracer)
private static readonly ActivitySource _activitySource = new("MyAPI.OrderService");

public async Task<Order> CreateOrder(OrderRequest request)
{
    using var activity = _activitySource.StartActivity("CreateOrder");
    activity?.SetTag("order.customer_id", request.CustomerId);
    activity?.SetTag("order.item_count", request.Items.Count);

    try
    {
        var order = await _repository.CreateAsync(request);
        activity?.SetTag("order.id", order.Id);
        return order;
    }
    catch (Exception ex)
    {
        activity?.SetStatus(ActivityStatusCode.Error, ex.Message);
        throw;
    }
}
```

## Custom metrics

```csharp
using System.Diagnostics.Metrics;

private static readonly Meter _meter = new("MyAPI.Orders");
private static readonly Counter<long> _ordersCreated = _meter.CreateCounter<long>("orders.created");
private static readonly Histogram<double> _orderProcessingTime = _meter.CreateHistogram<double>("orders.processing_time_ms");

public async Task<Order> CreateOrder(OrderRequest request)
{
    var sw = Stopwatch.StartNew();
    var order = await _repository.CreateAsync(request);
    sw.Stop();

    _ordersCreated.Add(1, new("type", request.Type));
    _orderProcessingTime.Record(sw.ElapsedMilliseconds);

    return order;
}
```
