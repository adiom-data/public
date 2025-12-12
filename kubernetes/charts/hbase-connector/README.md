# HBase Connector Helm Chart

This Helm chart deploys the HBase Kafka Connector as a StatefulSet with 1 replica. 

## Networking

The source HBase cluster (specifically, Region Servers), need to be able to directly connect to the HBase Kafka Connector. In cases where the source HBase cluster is deployed in a different environment that can't access internal Kubernetes hostnames, the HBase Kafka Connector can be configured with a specific hostname in hbase-site.xml (see templates/configmap.yaml):
```
    <property>
    <name>hbase.regionserver.hostname</name>
    <value>MY_EXTERNAL_HOSTNAME</value>
    </property>
    <property>
    <name>hbase.unsafe.regionserver.hostname</name>
    <value>MY_EXTERNAL_HOSTNAME</value>
    </property>
```

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

## Enabling Replication in HBase

* ensure hbase-site.xml has replication:true
* for all tables and column familes that need to be replicated, set REPLICATION_SCOPE to 1
* add_peer 'hbasekafka','SECOND_ZOOKEEPER:2181:/kafkaproxy'
* list_peers
* status 'replication'

Our command:
```
add_peer 'hbasekafka','zookeeper-headless:2181:/kafkaproxy'
```
For HBase-2 the syntax is a bit different:
```
add_peer 'hbasekafka',CLUSTER_KEY=>'zookeeper-headless:2181:/kafkaproxy'
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
