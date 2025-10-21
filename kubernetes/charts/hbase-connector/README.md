# HBase Connector Helm Chart

This Helm chart deploys the HBase Kafka Connector as a StatefulSet with 1 replica.

## Configuration

The chart exposes two main configuration variables:

### Required Values

- `kafka.broker`: Kafka broker address (default: `localhost:9092`)
- `zookeeper.quorum`: Zookeeper quorum string (default: `localhost:2181`)

### Example values.yaml

```yaml
kafka:
  broker: "kafka-broker:9092"

zookeeper:
  quorum: "zk1,zk2,zk3"
```

## Installation

```bash
# Install with default values
helm install my-hbase-connector ./hbase-connector

# Install with custom values
helm install my-hbase-connector ./hbase-connector \
  --set kafka.broker="kafka-broker:9092" \
  --set zookeeper.quorum="zk1,zk2,zk3"
```
How we run it:
```bash
helm install -n hbase my-hbase-connector ./hbase-connector \
  --set kafka.broker="kafka:9092" \
  --set zookeeper.quorum="zookeeper-headless"
```

## Docker Command

The connector runs with the following command:
```
hbase-connectors kafkaproxy -a -e -b KAFKA_BROKER:9092 -p hbasekafka
```

Where:
- `-a`: Enable async mode
- `-e`: Enable events
- `-b`: Kafka broker address
- `-p`: HBase peer name

## Configuration Files

The chart mounts configuration files to `/opt/hbase-connectors/conf/`:
- `hbase-site.xml`: HBase configuration with Zookeeper settings
- `kafka-route-rules.xml`: Kafka routing rules
- `kafka.properties`: Kafka connection properties