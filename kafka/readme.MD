# Kafka (KRaft) Quick Guide

This Kafka setup supports both:
- host access via `localhost:9092` / `127.0.0.1:9092`
- container-to-container access via `kafka:29092` (inside `kafka_default` network)

## Listener Model

- `EXTERNAL://127.0.0.1:9092` for host tools
- `INTERNAL://kafka:29092` for Docker services
- `CONTROLLER://:9093` for KRaft controller

`docker-compose.yml` uses:
- `KAFKA_LISTENERS=INTERNAL://:29092,EXTERNAL://:9092,CONTROLLER://:9093`
- `KAFKA_ADVERTISED_LISTENERS=INTERNAL://kafka:29092,EXTERNAL://127.0.0.1:9092`
- `KAFKA_INTER_BROKER_LISTENER_NAME=INTERNAL`

## Start Kafka

```bash
docker compose up -d
docker logs -f kafka-kraft
```

## Manage Topics From Host (No docker exec)

List:
```bash
docker run --rm --network host apache/kafka:3.7.0 \
  /opt/kafka/bin/kafka-topics.sh --list --bootstrap-server localhost:9092
```

Create gateway worker topic:
```bash
docker run --rm --network host apache/kafka:3.7.0 \
  /opt/kafka/bin/kafka-topics.sh --create \
  --topic tuseme.messages.send \
  --bootstrap-server localhost:9092 \
  --partitions 1 \
  --replication-factor 1
```

If topic already exists, Kafka returns `TopicExistsException` (safe to ignore).

## Tuseme Gateway Integration

Gateway worker subscribes to:
- `tuseme.messages.send`

Use this in `tuseme-gateway/.env`:
```env
KAFKA_BOOTSTRAP_SERVERS=kafka:29092
KAFKA_TOPIC_PREFIX=tuseme
KAFKA_CONSUMER_GROUP=tuseme-workers
```

`tuseme-gateway/docker-compose-prd.yml` must attach `api` and `worker` to external network `kafka_default`.

If you choose host-based Kafka instead, use:
```env
KAFKA_BOOTSTRAP_SERVERS=host.docker.internal:9092
```
and ensure advertised listeners are reachable from Docker containers.

## Troubleshooting

- If worker shows `Unable connect to "localhost:9092"`:
  - it is using wrong bootstrap address inside container
  - use `kafka:29092`, not `localhost:9092`
- If worker shows `Topic ... not found in cluster metadata`:
  - create `tuseme.messages.send`
- If compose warns about old kafka/redis/zookeeper containers:
  - run `docker compose -f docker-compose-prd.yml down --remove-orphans`