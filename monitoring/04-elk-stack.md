# ELK Stack

> Ngay bat dau: ___

## ELK la gi

```
Elasticsearch — Luu tru va search logs
Logstash      — Thu thap, parse, transform logs
Kibana        — Visualize va search logs
```

Alternative nhe hon: **PLG stack** (Promtail + Loki + Grafana)

## Cai dat ELK bang Docker

```yaml
services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:8.12.0
    environment:
      - discovery.type=single-node
      - xpack.security.enabled=false
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ports:
      - "9200:9200"
    volumes:
      - es_data:/usr/share/elasticsearch/data

  kibana:
    image: docker.elastic.co/kibana/kibana:8.12.0
    ports:
      - "5601:5601"
    depends_on:
      - elasticsearch

volumes:
  es_data:
```

## Elasticsearch queries co ban

```bash
# Kiem tra health
curl http://localhost:9200/_cluster/health?pretty

# List indices
curl http://localhost:9200/_cat/indices?v

# Search
curl -X GET "http://localhost:9200/logs-*/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": { "match": { "message": "error" } },
  "size": 10,
  "sort": [{ "@timestamp": "desc" }]
}'

# Count
curl "http://localhost:9200/logs-*/_count?pretty" -H 'Content-Type: application/json' -d'
{ "query": { "match": { "level": "error" } } }'
```

## Ship logs tu Docker

```yaml
# Filebeat container
services:
  filebeat:
    image: docker.elastic.co/beats/filebeat:8.12.0
    user: root
    volumes:
      - ./filebeat.yml:/usr/share/filebeat/filebeat.yml:ro
      - /var/lib/docker/containers:/var/lib/docker/containers:ro
      - /var/run/docker.sock:/var/run/docker.sock:ro
```

## Kibana

Truy cap: http://localhost:5601

1. Management → Stack Management → Data Views
2. Tao Data View: `logs-*`
3. Discover → Search va filter logs
4. Dashboard → Tao visualization
