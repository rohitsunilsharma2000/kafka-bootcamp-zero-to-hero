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

