A **step-by-step guide** to set up **Apache Kafka locally on your Mac laptop**, using either **Homebrew** or **Docker** (you can choose either one):

---

## ✅ Option 1: Install Kafka Using Homebrew (Recommended for Mac)

> Suitable if you want to run Kafka natively without Docker.

### **Step 1: Install Java (if not installed)**

```bash
brew install openjdk@17
```

### **Step 2: Add Java to your shell (if needed)**

```bash
echo 'export PATH="/opt/homebrew/opt/openjdk@17/bin:$PATH"' >> ~/.zprofile
source ~/.zprofile
```

### **Step 3: Install Kafka**

```bash
brew install kafka
```

### **Step 4: Start ZooKeeper (Kafka needs it)**

```bash
zookeeper-server-start /opt/homebrew/etc/kafka/zookeeper.properties
```

Open a new terminal tab for the next step.

### **Step 5: Start Kafka Server**

```bash
kafka-server-start /opt/homebrew/etc/kafka/server.properties
```

---

## ✅ Option 2: Install Kafka Using Docker (Simpler for quick testing)

### **Step 1: Install Docker (if not installed)**

Download: [https://www.docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop)

### **Step 2: Use Docker Compose File**

Create a file named `docker-compose.yml`:

```yaml
version: '3'
services:
  zookeeper:
    image: confluentinc/cp-zookeeper:7.5.0
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181

  kafka:
    image: confluentinc/cp-kafka:7.5.0
    ports:
      - "9092:9092"
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://localhost:9092
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
```

### **Step 3: Start Kafka**

```bash
docker-compose up -d
```

---

## ✅ Verify Kafka Is Running

### **Create a test topic**

```bash
kafka-topics --create --topic test-topic --bootstrap-server localhost:9092 --partitions 1 --replication-factor 1
```

### **Produce a message**

```bash
kafka-console-producer --topic test-topic --bootstrap-server localhost:9092
```

Type messages and press `Enter`.

### **Consume the message**

In another terminal:

```bash
kafka-console-consumer --topic test-topic --bootstrap-server localhost:9092 --from-beginning
```

---

## 🧰 Tips

| Task           | Command                                                                        |
| -------------- | ------------------------------------------------------------------------------ |
| List topics    | `kafka-topics --list --bootstrap-server localhost:9092`                        |
| Describe topic | `kafka-topics --describe --topic test-topic --bootstrap-server localhost:9092` |
| Delete topic   | `kafka-topics --delete --topic test-topic --bootstrap-server localhost:9092`   |

---

✅ How to Run Kafka Commands Inside Docker Container

To **test Exactly-Once Semantics (EOS)** in Kafka for a **Payment Processor scenario**, you must use:

* ✅ **Kafka transactional producer**
* ✅ **Kafka consumer in read-committed mode**
* ✅ **A database operation in the same transaction (simulated here)**

---

## 💳 Goal:

Ensure a payment is **processed exactly once**, even if the producer retries or consumer restarts.

---

## 🧱 Setup Requirements

* Kafka 2.5+ (supports EOS)
* Java or Spring Kafka app OR Kafka CLI with config overrides (limited)
* Transactions must be enabled with `transactional.id` and `enable.idempotence=true`

> ⚠️ Kafka CLI tools don’t support full EOS testing — use Java for full control. But here's what you can do using Kafka CLI + configs to simulate.

---

## 🔧 Step-by-Step with Commands

---

### 🔹 1. Start Kafka and Zookeeper (already done)

---

### 🔹 2. Create Topic for Payments

```sh
.\bin\windows\kafka-topics.bat --create --topic payments.initiated --bootstrap-server localhost:9092 --partitions 1 --replication-factor 1
```

```sh
.\bin\windows\kafka-topics.bat --create --topic payments.processed --bootstrap-server localhost:9092 --partitions 1 --replication-factor 1
```

---

### 🔹 3. Simulate Transactional Producer (CLI doesn’t support real transactions)

🛑 **Kafka CLI doesn’t support `transactional.id` or transactions.**

To simulate:

```sh
.\bin\windows\kafka-console-producer.bat ^
--broker-list localhost:9092 ^
--topic payments.initiated ^
--property "parse.key=true" ^
--property "key.separator=:"
```

Then input:

```
txn123:{"user":"U1", "amount": 100}
txn124:{"user":"U2", "amount": 150}
```

---

### 🔹 4. Consume in Read-Committed Mode

You can’t simulate read-committed using CLI — **only Kafka Java clients** can filter uncommitted messages.

---

### ✅ To Fully Test Exactly-Once Semantics — Use Java

Here’s the **Java config** for **transactional producer**:

```java
props.put(ProducerConfig.ENABLE_IDEMPOTENCE_CONFIG, "true");
props.put(ProducerConfig.TRANSACTIONAL_ID_CONFIG, "payment-tx-id");
```

And **read committed** consumer config:

```java
props.put(ConsumerConfig.ISOLATION_LEVEL_CONFIG, "read_committed");
```

Use `producer.initTransactions()`, `beginTransaction()`, then produce & commit atomically.

---

## 📌 Summary of Key Settings for EOS

| Component | Setting              | Value                        |
| --------- | -------------------- | ---------------------------- |
| Producer  | `enable.idempotence` | `true`                       |
| Producer  | `transactional.id`   | `your-unique-transaction-id` |
| Consumer  | `isolation.level`    | `read_committed`             |
| Consumer  | `enable.auto.commit` | `false`                      |
| Consumer  | Use manual commit    | `consumer.commitSync()`      |

---

Thanks for the details — you're using **Docker** and the containers are running successfully. The `zsh: command not found` error is expected because Kafka CLI tools like `kafka-topics`, `kafka-console-producer`, etc., are not installed on your Mac directly — they exist *inside the Kafka container*.

---

## ✅ How to Run Kafka Commands Inside Docker Container

You need to **exec into the Kafka container** and run the CLI commands from there:

### **Step 1: Enter the Kafka container**

```bash
docker exec -it kafka-bootcamp-kafka-1 bash
```

### **Step 2: Run Kafka CLI commands inside the container**

* **Create a topic**

  ```bash
  kafka-topics --create --topic test-topic --bootstrap-server localhost:9092 --partitions 1 --replication-factor 1
  ```

* **List topics**

  ```bash
  kafka-topics --list --bootstrap-server localhost:9092
  ```

* **Produce messages**

  ```bash
  kafka-console-producer --topic test-topic --bootstrap-server localhost:9092
  ```

* **Consume messages**

  ```bash
  kafka-console-consumer --topic test-topic --bootstrap-server localhost:9092 --from-beginning
  ```

---

## 🧠 Pro Tip

If you want to avoid entering the container each time, you can run a one-liner like this directly from your host terminal:

```bash
docker exec -it kafka-bootcamp-kafka-1 kafka-topics --list --bootstrap-server localhost:9092
```

---

# Shell script to automate common Kafka Docker commands from your Mac terminal:-



---

### ✅ Save this as: `kafka-docker.sh`

```bash
#!/bin/bash

# Automatically find the Kafka container name using the image
KAFKA_CONTAINER=$(docker ps --filter "ancestor=confluentinc/cp-kafka:7.5.0" --format "{{.Names}}" | head -n 1)

if [ -z "$KAFKA_CONTAINER" ]; then
  echo "❌ Kafka container not running!"
  exit 1
fi

# 🔹 Create a topic with default settings
create_topic() {
  if [ -z "$1" ]; then
    echo "⚠️ Please provide a topic name"
    exit 1
  fi
  echo "🔧 Creating topic '$1'..."
  docker exec -it "$KAFKA_CONTAINER" kafka-topics \
    --create \
    --topic "$1" \
    --bootstrap-server localhost:9092 \
    --partitions 1 \
    --replication-factor 1
}

# 🔹 List all Kafka topics
list_topics() {
  echo "📋 Listing all topics..."
  docker exec -it "$KAFKA_CONTAINER" kafka-topics \
    --list \
    --bootstrap-server localhost:9092
}

# 🔹 Open interactive producer console
produce_messages() {
  if [ -z "$1" ]; then
    echo "⚠️ Please provide a topic name"
    exit 1
  fi
  echo "📝 Producing to topic '$1' (Press Ctrl+C to exit)..."
  docker exec -it "$KAFKA_CONTAINER" kafka-console-producer \
    --topic "$1" \
    --bootstrap-server localhost:9092
}

# 🔹 Open interactive consumer console
consume_messages() {
  if [ -z "$1" ]; then
    echo "⚠️ Please provide a topic name"
    exit 1
  fi
  echo "👀 Consuming from topic '$1' (Press Ctrl+C to exit)..."
  docker exec -it "$KAFKA_CONTAINER" kafka-console-consumer \
    --topic "$1" \
    --bootstrap-server localhost:9092 \
    --from-beginning
}

# 🔹 Describe a topic
describe_topic() {
  if [ -z "$1" ]; then
    echo "⚠️ Please provide a topic name"
    exit 1
  fi
  echo "🔍 Describing topic '$1'..."
  docker exec -it "$KAFKA_CONTAINER" kafka-topics \
    --describe \
    --topic "$1" \
    --bootstrap-server localhost:9092
}

# 🔹 Delete a topic
delete_topic() {
  if [ -z "$1" ]; then
    echo "⚠️ Please provide a topic name"
    exit 1
  fi
  echo "❌ Deleting topic '$1'..."
  docker exec -it "$KAFKA_CONTAINER" kafka-topics \
    --delete \
    --topic "$1" \
    --bootstrap-server localhost:9092
}

# 🔹 Produce 5 test messages automatically
test_produce() {
  if [ -z "$1" ]; then
    echo "⚠️ Please provide a topic name"
    exit 1
  fi
  echo "🚀 Sending 5 test messages to topic '$1'..."
  for i in {1..5}; do
    echo "Test Message $i" | docker exec -i "$KAFKA_CONTAINER" kafka-console-producer \
      --topic "$1" \
      --bootstrap-server localhost:9092 > /dev/null
  done
  echo "✅ Done!"
}

# 🔹 Usage instructions
usage() {
  echo ""
  echo "🧰 Kafka Docker Helper Script"
  echo ""
  echo "Usage:"
  echo "  ./kafka-docker.sh create <topic-name>       → Create topic"
  echo "  ./kafka-docker.sh list                      → List all topics"
  echo "  ./kafka-docker.sh produce <topic-name>      → Produce messages (interactive)"
  echo "  ./kafka-docker.sh consume <topic-name>      → Consume messages (from beginning)"
  echo "  ./kafka-docker.sh describe <topic-name>     → Describe topic"
  echo "  ./kafka-docker.sh delete <topic-name>       → Delete topic"
  echo "  ./kafka-docker.sh test-produce <topic-name> → Send 5 test messages"
  echo ""
}

# 🔹 Command routing
COMMAND=$1
TOPIC=$2

case "$COMMAND" in
  create) create_topic "$TOPIC" ;;
  list) list_topics ;;
  produce) produce_messages "$TOPIC" ;;
  consume) consume_messages "$TOPIC" ;;
  describe) describe_topic "$TOPIC" ;;
  delete) delete_topic "$TOPIC" ;;
  test-produce) test_produce "$TOPIC" ;;
  *) usage ;;
esac

```

---

### ✅ Make it Executable

```bash
chmod +x kafka-docker.sh
```

---

### ✅ Example Usages

```bash

# Step 1: Create a topic
./kafka-docker.sh create orders

# Step 2: List all topics
./kafka-docker.sh list

# Step 3: Describe the topic
./kafka-docker.sh describe orders

# Step 4: Send 5 automatic test messages
./kafka-docker.sh test-produce orders

# Step 5: Consume messages from the topic
./kafka-docker.sh consume orders

# Step 6: Produce messages manually
./kafka-docker.sh produce orders

# Step 7: Delete the topic
./kafka-docker.sh delete orders


```

---




