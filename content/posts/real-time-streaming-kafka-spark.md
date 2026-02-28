---
title: "Real-Time Streaming with Apache Kafka and Spark"
date: 2026-02-25T11:00:00Z
draft: false
description: "Designing real-time data streaming pipelines with Kafka and Spark Structured Streaming — Lorem Ipsum placeholder content for layout demonstration."
tags: ["streaming", "kafka", "spark"]
---

## The Case for Real-Time

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.

Batch processing is no longer enough for modern analytics. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.

## Kafka Fundamentals

Sed ut perspiciatis unde omnis iste natus error sit voluptatem accusantium doloremque laudantium, totam rem aperiam, eaque ipsa quae ab illo inventore veritatis et quasi architecto beatae vitae dicta sunt explicabo.

### Topic Design

Nemo enim ipsam voluptatem quia voluptas sit aspernatur aut odit aut fugit, sed quia consequuntur magni dolores eos qui ratione voluptatem sequi nesciunt.

```yaml
# topic-config.yaml
topics:
  - name: events.clickstream.raw
    partitions: 12
    replication_factor: 3
    config:
      retention.ms: 604800000     # 7 days
      cleanup.policy: delete
      compression.type: zstd

  - name: events.clickstream.enriched
    partitions: 12
    replication_factor: 3
    config:
      retention.ms: 2592000000    # 30 days
      cleanup.policy: compact,delete
```

### Schema Registry

At vero eos et accusamus et iusto odio dignissimos ducimus qui blanditiis praesentium voluptatum deleniti atque corrupti quos dolores et quas molestias excepturi sint occaecati cupiditate non provident.

```json
{
  "type": "record",
  "name": "ClickEvent",
  "namespace": "com.example.events",
  "fields": [
    { "name": "event_id", "type": "string" },
    { "name": "user_id", "type": "string" },
    { "name": "page_url", "type": "string" },
    { "name": "timestamp", "type": "long", "logicalType": "timestamp-millis" },
    { "name": "session_id", "type": ["null", "string"], "default": null }
  ]
}
```

## Spark Structured Streaming

Similique sunt in culpa qui officia deserunt mollitia animi, id est laborum et dolorum fuga. Et harum quidem rerum facilis est et expedita distinctio.

```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import from_json, col, window, count

spark = SparkSession.builder \
    .appName("clickstream-aggregator") \
    .getOrCreate()

schema = "event_id STRING, user_id STRING, page_url STRING, timestamp LONG, session_id STRING"

raw_stream = (
    spark.readStream
    .format("kafka")
    .option("kafka.bootstrap.servers", "kafka-broker:9092")
    .option("subscribe", "events.clickstream.raw")
    .option("startingOffsets", "latest")
    .load()
    .selectExpr("CAST(value AS STRING) as json_str")
    .select(from_json(col("json_str"), schema).alias("data"))
    .select("data.*")
)

page_views = (
    raw_stream
    .withWatermark("timestamp", "5 minutes")
    .groupBy(
        window(col("timestamp"), "1 minute"),
        col("page_url")
    )
    .agg(count("*").alias("view_count"))
)

page_views.writeStream \
    .format("delta") \
    .outputMode("append") \
    .option("checkpointLocation", "/mnt/checkpoints/page_views") \
    .toTable("silver.page_views_1min")
```

## Monitoring the Pipeline

Nam libero tempore, cum soluta nobis est eligendi optio cumque nihil impedit quo minus id quod maxime placeat facere possimus, omnis voluptas assumenda est, omnis dolor repellendus.

| Metric | Target | Alert |
|--------|--------|-------|
| Consumer lag | < 1000 msgs | PagerDuty if > 5000 for 5 min |
| Processing latency (p95) | < 30s | Slack if > 60s |
| Throughput | > 10k events/s | Dashboard only |
| Checkpoint duration | < 10s | Warn if > 30s |

### Dead Letter Queues

Temporibus autem quibusdam et aut officiis debitis aut rerum necessitatibus saepe eveniet ut et voluptates repudiandae sint et molestiae non recusandae.

> Never silently drop bad records. Route them to a dead letter topic, tag them with the failure reason, and reprocess once the root cause is fixed.

## Exactly-Once Semantics

Itaque earum rerum hic tenetur a sapiente delectus, ut aut reiciendis voluptatibus maiores alias consequatur aut perferendis doloribus asperiores repellat. Quis autem vel eum iure reprehenderit qui in ea voluptate velit esse quam nihil molestiae consequatur.

Vel illum qui dolorem eum fugiat quo voluptas nulla pariatur. Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.

## Conclusion

Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
