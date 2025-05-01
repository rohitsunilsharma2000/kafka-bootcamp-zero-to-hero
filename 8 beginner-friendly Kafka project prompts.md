 **8 beginner-friendly Kafka project prompts**, each carefully crafted with:

- ✅ **Detailed explanation**
- 🛠️ **Tech stack**
- 📋 **Rules to follow** (Kafka-specific, Code Quality & Architecture, Testing)

This is ideal for **Kafka beginners** using **Spring Boot** and wanting to grow systematically.

---

## 🔹 **1. Exactly-Once Semantics — Payment Processor**

### **Project Name:** `PaymentProcessorService`

### **Goal:**
Ensure a payment is processed exactly once — never duplicated — using Kafka's idempotent producer + transaction support.

### **Tech Stack:**
- Java 17
- Spring Boot
- Spring Kafka
- PostgreSQL
- Kafka (Docker)
- Testcontainers

### **How It Works:**
1. Producer sends payment events to `payments.initiated`.
2. Consumer reads the event **within a transaction**:
   - Writes to DB.
   - Publishes confirmation to `payments.processed`.

### **Rules to Follow:**

**Kafka-Specific Rules:**
- Enable idempotence: `enable.idempotence=true`
- Use transactional producer
- Use consistent topic naming (`payments.initiated`, `payments.processed`)
- Graceful shutdown with `@PreDestroy`

**Code Quality:**
- Separate payment logic from Kafka listener.
- Use `@Transactional` for DB+Kafka boundary.

**Testing:**
- Unit: Validate producer sends correct keys/values.
- Integration: Transaction boundary test — message + DB commit must both succeed.
- Negative: Simulate DB failure → message must not commit.

---

## 🔹 **2. Schema Evolution — User Signup Events**

### **Project Name:** `UserSignupPipeline`

### **Goal:**
Handle changes in user event structure without breaking consumers.

### **Tech Stack:**
- Java 17
- Spring Boot
- Kafka + Confluent Schema Registry
- Avro
- spring-cloud-stream (optional)

### **How It Works:**
1. Service A produces `user.signup.v1` event with fields: name, email.
2. Service B consumes and logs/stores.
3. You evolve schema by adding `phoneNumber`.

### **Rules to Follow:**

**Kafka-Specific:**
- Register schema in Schema Registry
- Ensure `BACKWARD` compatibility

**Code Quality:**
- Use Avro-generated DTOs
- Keep schema evolution history

**Testing:**
- Unit: Schema serialization test
- Integration: Consume old → evolve → consume new
- Negative: Register incompatible schema → catch exception

---

## 🔹 **3. Partition Management — Log Ingestion System**

### **Project Name:** `MultiAppLogIngestor`

### **Goal:**
Distribute log ingestion across 50+ partitions using appName as the key.

### **Tech Stack:**
- Java 17
- Spring Boot
- Spring Kafka
- Prometheus + Grafana
- Kafka (Docker)

### **How It Works:**
- Simulated 100 apps produce logs to `logs.events` using key = `appName`.
- Multiple consumers read in a balanced way.

### **Rules to Follow:**

**Kafka-Specific:**
- Partition by appName
- Monitor consumer lag
- Set topic retention appropriately

**Code Quality:**
- Producer utility to send random logs
- Separate consumers by groupId

**Testing:**
- Unit: Producer sends to correct partition
- Integration: Verify partition balance
- Observability: Prometheus metrics on lag

---

## 🔹 **4. Handling Backpressure — Video Processor**

### **Project Name:** `VideoProcessingService`

### **Goal:**
Simulate slow consumers and implement rate control to avoid lag buildup.

### **Tech Stack:**
- Java 17
- Spring Boot
- Spring Kafka
- MongoDB or Filesystem
- Kafka + Prometheus + Grafana

### **How It Works:**
- `video.uploaded` topic contains video processing tasks.
- Consumer simulates 3-5 sec delay → you add monitoring and retry logic.

### **Rules to Follow:**

**Kafka-Specific:**
- Track consumer lag
- Use `max.poll.records`, `max.poll.interval.ms` wisely

**Code Quality:**
- Retry logic with exponential backoff
- DLQ for failed videos

**Testing:**
- Unit: Delay simulation
- Integration: Lag threshold alert
- Negative: Force timeout → DLQ routing

---

## 🔹 **5. Distributed Tracing — Order Flow Tracker**

### **Project Name:** `OrderTrackingSystem`

### **Goal:**
Trace the entire order lifecycle via Kafka headers with correlation ID.

### **Tech Stack:**
- Java 17
- Spring Boot
- Spring Kafka
- Sleuth + OpenTelemetry
- Zipkin or Jaeger

### **How It Works:**
1. `OrderService` → produces to `orders.new`
2. `InventoryService` → consumes → responds on `inventory.status`
3. `NotificationService` → final stage

Each service logs with trace ID.

### **Rules to Follow:**

**Kafka-Specific:**
- Add correlation ID to headers
- Maintain consistent topic naming

**Code Quality:**
- Use interceptors or filters to inject correlation ID
- Avoid log pollution; use MDC

**Testing:**
- Unit: Validate headers
- Integration: End-to-end trace via Jaeger

---

## 🔹 **6. DLQ & Retry — User Signup Validator**

### **Project Name:** `SignupValidatorService`

### **Goal:**
Validate signup data. On failure, retry once then route to `user.signup.dlq`.

### **Tech Stack:**
- Java 17
- Spring Boot
- Spring Kafka
- Kafka Retry Template or Manual Retry Logic

### **How It Works:**
- Message fails (e.g., email missing) → retry → DLQ if still failing

### **Rules to Follow:**

**Kafka-Specific:**
- Use separate DLQ topic
- Backoff retry config

**Code Quality:**
- Centralized error classification
- Retry only transient errors

**Testing:**
- Unit: Retry logic
- Negative: Permanent error → assert DLQ push
- Integration: Test both retry paths

---

## 🔹 **7. Cross-Cluster Replication — Order Mirroring**

### **Project Name:** `MultiRegionOrderReplicator`

### **Goal:**
Replicate `orders.local` to `orders.remote` using MirrorMaker or Kafka Connect.

### **Tech Stack:**
- Java 17
- Spring Boot (for producer/consumer)
- Kafka MirrorMaker
- Docker Compose with 2 Kafka clusters

### **How It Works:**
- Service A → `orders.local`
- Replicator → `orders.remote`
- Consumer on remote cluster validates order data

### **Rules to Follow:**

**Kafka-Specific:**
- Use same topic name across clusters
- Offset alignment enabled
- Monitor replication lag

**Code Quality:**
- Validate data before consumption
- Resilient fallbacks in case replication breaks

**Testing:**
- Integration: Check remote consumer consistency
- Failure: Replicator down → local buffer test

---

## 🔹 **8. Kafka Streams — Fraud Detection**

### **Project Name:** `RealTimeFraudDetector`

### **Goal:**
Detect suspicious transaction patterns (e.g., 3+ txns in 5 seconds) using Kafka Streams.

### **Tech Stack:**
- Java 17
- Spring Boot
- Kafka Streams DSL
- `spring-kafka-streams`
- TopologyTestDriver

### **How It Works:**
- Ingest from `transactions.raw`
- Aggregate by userId over 5-sec windows
- Emit alert to `fraud.alerts`

### **Rules to Follow:**

**Kafka-Specific:**
- Use time-windowed aggregations
- Configure state store cleanup
- Handle late arrivals via grace period

**Code Quality:**
- Stream logic in `@Bean` method
- Use POJO and Serde abstraction

**Testing:**
- Unit: TopologyTestDriver for stream logic
- Integration: End-to-end with time simulation

---

