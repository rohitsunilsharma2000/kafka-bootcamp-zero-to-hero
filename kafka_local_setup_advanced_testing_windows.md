A **step-by-step guide** to set up **Apache Kafka locally on a Windows laptop**, along with **basic to advanced-level commands** (queries) for testing and learning purposes.

---

## ✅ Step 1: Install Java

Kafka needs Java (JDK 8 or newer).

1. Download and install JDK: [https://adoptium.net](https://adoptium.net)
2. Set environment variables:
   - Add `JAVA_HOME` and point it to your JDK folder.
   - Add `%JAVA_HOME%\bin` to your `PATH`.

Verify installation:

```sh
java -version
```

---

## ✅ Step 2: Download and Extract Kafka

1. Go to: [https://kafka.apache.org/downloads](https://kafka.apache.org/downloads)
2. Choose the latest **binary with Scala 2.13**.
3. Extract it, e.g., to: `C:\kafka`

---

## ✅ Step 3: Start Kafka (Single-node Local Setup)

1. Open **Command Prompt** and navigate to Kafka directory:

```sh
cd C:\kafka
```

2. Start **Zookeeper**:

```sh
.\bin\windows\zookeeper-server-start.bat .\config\zookeeper.properties
```

3. Open a **new Command Prompt** and start **Kafka broker**:

```sh
.\bin\windows\kafka-server-start.bat .\config\server.properties
```

---

## ✅ Step 4: Create a Topic

In a **new Command Prompt window**:

```sh
.\bin\windows\kafka-topics.bat --create --topic test-topic --bootstrap-server localhost:9092 --partitions 1 --replication-factor 1
```

---

## ✅ Step 5: List Topics

```sh
.\bin\windows\kafka-topics.bat --list --bootstrap-server localhost:9092
```

---

## ✅ Step 6: Produce Messages

```sh
.\bin\windows\kafka-console-producer.bat --broker-list localhost:9092 --topic test-topic
```

Type messages and press Enter.

---

## ✅ Step 7: Consume Messages

Open another terminal:

```sh
.\bin\windows\kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic test-topic --from-beginning
```

---

## ✅ Step 8: Advanced Kafka Queries

### 🔸 Describe a Topic

```sh
.\bin\windows\kafka-topics.bat --describe --topic test-topic --bootstrap-server localhost:9092
```

### 🔸 Delete a Topic (optional)

```sh
.\bin\windows\kafka-topics.bat --delete --topic test-topic --bootstrap-server localhost:9092
```

### 🔸 Send Messages with Key

```sh
.\bin\windows\kafka-console-producer.bat --broker-list localhost:9092 --topic test-topic --property "parse.key=true" --property "key.separator=:"
```

Type:
```
key1:Hello
key2:Kafka
```

### 🔸 Consume with Key Display

```sh
.\bin\windows\kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic test-topic --from-beginning --property print.key=true --property key.separator=:
```

### 🔸 Reset Offset (consume again from beginning)

```sh
.\bin\windows\kafka-consumer-groups.bat --bootstrap-server localhost:9092 --group test-group --topic test-topic --reset-offsets --to-earliest --execute
```

---

## ✅ Step 9: Shut Down Kafka

1. Stop Kafka broker (Ctrl+C)
2. Stop Zookeeper (Ctrl+C)


---
