
## 🧠 Kafka Advanced Commands & Use Cases (CLI-Based)

### 🔸 1. **Create Topic with Multiple Partitions & Custom Configs**

```sh
.\bin\windows\kafka-topics.bat ^
--create ^
--bootstrap-server localhost:9092 ^
--replication-factor 1 ^
--partitions 5 ^
--topic orders ^
--config retention.ms=3600000 ^
--config segment.bytes=1073741824
```

📝 This creates a topic `orders` with 5 partitions and 1-hour retention.

---

### 🔸 2. **List All Consumer Groups**

```sh
.\bin\windows\kafka-consumer-groups.bat --bootstrap-server localhost:9092 --list
```

---

### 🔸 3. **Describe a Specific Consumer Group**

```sh
.\bin\windows\kafka-consumer-groups.bat --bootstrap-server localhost:9092 --describe --group test-group
```

---

### 🔸 4. **Consume From Specific Partition**

```sh
.\bin\windows\kafka-console-consumer.bat ^
--bootstrap-server localhost:9092 ^
--topic orders ^
--partition 2 ^
--offset 0
```

📝 Reads from partition 2 starting at offset 0.

---

### 🔸 5. **Produce Message with Partition Key Logic**

```sh
.\bin\windows\kafka-console-producer.bat ^
--broker-list localhost:9092 ^
--topic orders ^
--property "parse.key=true" ^
--property "key.separator=:"
```

Then type:
```
user1:Buy Item A
user2:Buy Item B
```

Kafka will hash keys (`user1`, `user2`) to determine partition placement.

---

### 🔸 6. **Read Messages with Timestamp (Starting from Specific Time)**

Get messages from a timestamp (requires a custom tool or Kafka consumer API, not CLI directly). But you can reset offsets for a consumer group to a timestamp like this:

```sh
.\bin\windows\kafka-consumer-groups.bat ^
--bootstrap-server localhost:9092 ^
--group test-group ^
--topic orders ^
--reset-offsets ^
--to-datetime 2024-12-31T23:59:59.000 ^
--execute
```

---

### 🔸 7. **Delete Topic (Force)**

```sh
.\bin\windows\kafka-topics.bat ^
--bootstrap-server localhost:9092 ^
--delete --topic orders
```

---

### 🔸 8. **Manually Set Offsets for a Consumer Group**

```sh
.\bin\windows\kafka-consumer-groups.bat ^
--bootstrap-server localhost:9092 ^
--group test-group ^
--topic orders:0 ^
--reset-offsets ^
--to-offset 10 ^
--execute
```

📝 Sets partition 0 to start consuming from offset 10.

---

### 🔸 9. **View Kafka Broker Config**

```sh
.\bin\windows\kafka-configs.bat ^
--bootstrap-server localhost:9092 ^
--entity-type brokers ^
--entity-name 0 ^
--describe
```

---

### 🔸 10. **Update Topic Config (Retention Time)**

```sh
.\bin\windows\kafka-configs.bat ^
--bootstrap-server localhost:9092 ^
--entity-type topics ^
--entity-name orders ^
--alter ^
--add-config retention.ms=7200000
```

📝 Updates the topic to retain messages for 2 hours.

---

### 🔸 11. **Compact Topic Example Setup**

```sh
.\bin\windows\kafka-topics.bat ^
--create ^
--bootstrap-server localhost:9092 ^
--replication-factor 1 ^
--partitions 1 ^
--topic compacted-topic ^
--config cleanup.policy=compact
```

Then produce messages with keys:

```sh
.\bin\windows\kafka-console-producer.bat ^
--broker-list localhost:9092 ^
--topic compacted-topic ^
--property "parse.key=true" ^
--property "key.separator=:"
```

Send:
```
item1:old_value
item1:new_value
```

📝 Kafka will **compact** the topic and retain only the **latest value for each key**.

---

### 🔸 12. **Create a Topic with Log Compaction and Min Cleanable Dirty Ratio**

```sh
.\bin\windows\kafka-topics.bat ^
--create ^
--bootstrap-server localhost:9092 ^
--replication-factor 1 ^
--partitions 2 ^
--topic user-updates ^
--config cleanup.policy=compact ^
--config min.cleanable.dirty.ratio=0.1
```

📝 This ensures Kafka compacts logs aggressively once the log becomes 10% "dirty" (i.e., eligible for compaction).

---

### 🔸 13. **List All Configurable Properties of a Topic**

```sh
.\bin\windows\kafka-configs.bat ^
--bootstrap-server localhost:9092 ^
--entity-type topics ^
--entity-name user-updates ^
--describe
```

📝 Useful to verify cleanup policy, retention, segment size, etc.

---

### 🔸 14. **Simulate Consumer Lag**

1. Produce a few messages to a topic:

```sh
.\bin\windows\kafka-console-producer.bat --broker-list localhost:9092 --topic lag-topic
```

2. Consume only a few (don’t use `--from-beginning`):

```sh
.\bin\windows\kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic lag-topic --group lag-group
```

3. Now list lag:

```sh
.\bin\windows\kafka-consumer-groups.bat --bootstrap-server localhost:9092 --describe --group lag-group
```

📝 Helps you understand how much data a consumer group is behind the producer.

---

### 🔸 15. **Change Default Partition Count of New Topics**

```sh
.\bin\windows\kafka-configs.bat ^
--bootstrap-server localhost:9092 ^
--entity-type brokers ^
--entity-name 0 ^
--alter ^
--add-config num.partitions=6
```

📝 All new topics will now be created with 6 partitions unless overridden.

---

### 🔸 16. **Reassign Topic Partitions (Partition Rebalancing)**

1. Generate reassignment JSON:

```sh
.\bin\windows\kafka-reassign-partitions.bat ^
--bootstrap-server localhost:9092 ^
--generate ^
--topics-to-move-json-file topics-to-move.json ^
--broker-list "0" ^
--output-file reassignment.json
```

2. Apply the reassignment:

```sh
.\bin\windows\kafka-reassign-partitions.bat ^
--bootstrap-server localhost:9092 ^
--reassignment-json-file reassignment.json ^
--execute
```

> 🔍 `topics-to-move.json` sample:
```json
{
  "topics": [{"topic": "orders"}],
  "version": 1
}
```

📝 This is useful in multi-broker clusters, but you can simulate it in a single-broker test by reassigning to the same broker.

---

### 🔸 17. **Create a Topic with Compression Enabled**

```sh
.\bin\windows\kafka-topics.bat ^
--create ^
--bootstrap-server localhost:9092 ^
--replication-factor 1 ^
--partitions 2 ^
--topic compressed-topic ^
--config compression.type=gzip
```

📝 Helps save disk/network usage in testing environments with large messages.

---

### 🔸 18. **Manually Send Binary Data or Large JSON**

If you want to test with a large message:
```sh
type bigfile.json | .\bin\windows\kafka-console-producer.bat --broker-list localhost:9092 --topic test-topic
```

📝 This allows you to test how Kafka handles large payloads.

---

### 🔸 19. **Simulate Message Drop with `acks=0`**

```sh
.\bin\windows\kafka-console-producer.bat ^
--broker-list localhost:9092 ^
--topic test-topic ^
--producer-property acks=0
```

📝 Kafka won’t wait for acknowledgment, simulating fire-and-forget — good to test loss scenarios.

---

### 🔸 20. **Test Message Ordering by Producing to Fixed Partition**

```sh
.\bin\windows\kafka-console-producer.bat ^
--broker-list localhost:9092 ^
--topic ordered-topic ^
--property "partitioner.class=org.apache.kafka.clients.producer.internals.DefaultPartitioner"
```

Then control ordering by sending all messages with same key — Kafka guarantees ordering **within the same partition**.

---

### 🔸 21. **Consume Messages with Max Record Limit**

```sh
.\bin\windows\kafka-console-consumer.bat ^
--bootstrap-server localhost:9092 ^
--topic orders ^
--from-beginning ^
--max-messages 5
```

📝 Useful for unit tests or verifying message delivery.

---

### 🔸 22. **Consume Messages and Save to File**

```sh
.\bin\windows\kafka-console-consumer.bat ^
--bootstrap-server localhost:9092 ^
--topic orders ^
--from-beginning > output.txt
```

📝 Great for debugging or snapshotting messages.

---

