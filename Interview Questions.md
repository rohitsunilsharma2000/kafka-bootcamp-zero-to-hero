Here are **Top 10 Scenario-Based Apache Kafka Interview Questions**, ranging from **basic to complex** level, commonly asked for **Java backend/Kafka developer roles**:

---

## 🔹 **Basic Level (1–3): Understanding Core Kafka Concepts**

### **1. What happens if a Kafka consumer crashes while processing a message? How do you ensure no message is lost or reprocessed?**

✅ *Concepts:* Offset management, auto commit, manual commit
📌 *Follow-up:* Explain `enable.auto.commit`, `commitSync()` vs `commitAsync()`

---

### **2. Can Kafka retain messages even after they are consumed?**

✅ *Concepts:* Retention policy vs log compaction
📌 *Scenario:* A downstream system wants to reprocess 7-day old data

---

### **3. How do Kafka partitions improve scalability? What are the trade-offs of increasing partition count?**

✅ *Concepts:* Partitioning strategy, ordering guarantees, consumer parallelism
📌 *Scenario:* Topic A has 50 partitions, but you need strict ordering

---

## 🔹 **Intermediate Level (4–7): Production Use Cases & Failures**

### **4. You have 10 Kafka consumers in a group but only 3 partitions. What happens?**

✅ *Concepts:* Consumer group rebalancing, idle consumers
📌 *Scenario:* Discuss pros/cons of adding more partitions or consumers

---

### **5. How would you ensure *exactly-once processing* of messages in Kafka?**

✅ *Concepts:* Idempotent producer, Kafka transactions, consumer retries
📌 *Scenario:* A payment service processes user transactions via Kafka

---

### **6. A Kafka consumer is reprocessing messages multiple times. What could be the reasons?**

✅ *Concepts:* Offset commit strategy, processing exceptions, retries
📌 *Scenario:* Consumer crashes after processing but before commit

---

### **7. A message is published but not visible to the consumer. What could be wrong?**

✅ *Concepts:* Uncommitted transactions, log retention, topic configuration
📌 *Scenario:* Message seen in Kafka UI but not consumed by service

---

## 🔹 **Advanced Level (8–10): Performance, Resilience & Architecture**

### **8. How would you design a Kafka-based system to handle 1 million events per second with minimal delay?**

✅ *Concepts:* Batching, compression, partition tuning, acks
📌 *Scenario:* Ingest IoT sensor data with <100ms latency requirement

---

### **9. How do you handle dead letter queues (DLQs) in Kafka?**

✅ *Concepts:* Error handling, retries, Kafka Streams or consumer patterns
📌 *Scenario:* Failing messages must be rerouted and reviewed later

---

### **10. How would you monitor Kafka consumer lag and handle backpressure in real time?**

✅ *Concepts:* `ConsumerLag`, `max.poll.records`, Prometheus, alerts
📌 *Scenario:* Prevent OOM when consumer lags behind producer

---

## 🔹 **11. How do you ensure message ordering across multiple partitions for the same user or entity?**

✅ *Concepts:* Custom partitioner, message keys, partition affinity
📌 *Scenario:* Order events for a user must be processed in sequence

---

## 🔹 **12. How would you implement a retry mechanism for failed Kafka message processing?**

✅ *Concepts:* Retry topics, backoff strategies, DLQ
📌 *Scenario:* Transient DB failures during consumer processing

---

## 🔹 **13. Your Kafka topic has high lag even though consumers seem healthy. What could be the cause?**

✅ *Concepts:* Lag diagnosis, message size, consumer poll loop, GC pauses
📌 *Scenario:* Spikes in producer rate cause system-wide lag

---

## 🔹 **14. What happens when you increase the number of partitions in a topic from 3 to 6?**

✅ *Concepts:* Data redistribution, ordering impact, consumer rebalancing
📌 *Scenario:* Order-sensitive topic needs scaling without breaking processing logic

---

## 🔹 **15. How do you implement multi-region Kafka replication and failover?**

✅ *Concepts:* MirrorMaker 2.0, active-passive vs active-active
📌 *Scenario:* Disaster recovery for Kafka across AWS regions

---

## 🔹 **16. How do you handle schema evolution in Kafka when consuming Avro or Protobuf messages?**

✅ *Concepts:* Schema Registry, backward/forward compatibility
📌 *Scenario:* Adding a new field to a production schema

---

## 🔹 **17. A consumer keeps getting `OffsetOutOfRangeException`. What’s happening and how do you fix it?**

✅ *Concepts:* Retention policies, reset strategies
📌 *Scenario:* Consumer starts too late; topic data expired

---

## 🔹 **18. How do you design an idempotent Kafka consumer?**

✅ *Concepts:* Message deduplication, external ID stores, transactional outbox
📌 *Scenario:* A billing system must not double-charge a customer

---

## 🔹 **19. How would you secure a Kafka cluster in production?**

✅ *Concepts:* TLS, SASL, ACLs, encryption
📌 *Scenario:* You need to isolate teams and encrypt all traffic

---

## 🔹 **20. You want to trace an event across microservices that use Kafka. How do you implement distributed tracing?**

✅ *Concepts:* Correlation ID, OpenTelemetry, Kafka headers
📌 *Scenario:* Trace an order event through 5 services in a pipeline

---
