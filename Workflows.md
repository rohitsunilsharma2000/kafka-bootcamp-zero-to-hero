Below are **5 practical Kafka Docker script workflows** using your `kafka-docker.sh` tool — ideal for **testing**, **debugging**, and **simulating real-world flows**.

---

## 🧩 Workflow 1: Basic Topic Lifecycle

```bash
./kafka-docker.sh create user-events
./kafka-docker.sh list
./kafka-docker.sh describe user-events
./kafka-docker.sh test-produce user-events
./kafka-docker.sh consume user-events
./kafka-docker.sh delete user-events
```

🔹 **Use case:** Ideal for verifying Kafka installation and topic setup end-to-end.

---

## 🔄 Workflow 2: Continuous Producer + Consumer Debugging

```bash
./kafka-docker.sh create debug-topic
# Terminal 1
./kafka-docker.sh produce debug-topic
# Terminal 2
./kafka-docker.sh consume debug-topic
```

🧪 **Use case:** Simulate a live stream — useful while building Kafka apps.

---

## 🛒 Workflow 3: Simulate Order Processing (like eCommerce)

```bash
./kafka-docker.sh create orders
./kafka-docker.sh create payments
./kafka-docker.sh test-produce orders
./kafka-docker.sh test-produce payments
./kafka-docker.sh consume orders
./kafka-docker.sh consume payments
```

📦 **Use case:** Test producer/consumer logic for different services.

---

## 🚀 Workflow 4: High-Level Automation Testing

```bash
for topic in logs metrics alerts; do
  ./kafka-docker.sh create "$topic"
  ./kafka-docker.sh test-produce "$topic"
  ./kafka-docker.sh describe "$topic"
done
```

🧰 **Use case:** Test multiple topics in one go for a monitoring pipeline.

---

## 🧼 Workflow 5: Cleanup and Retest

```bash
# Recreate same topic to reset state
./kafka-docker.sh delete my-temp-topic
sleep 2
./kafka-docker.sh create my-temp-topic
./kafka-docker.sh test-produce my-temp-topic
./kafka-docker.sh consume my-temp-topic
```

🧪 **Use case:** Reset and re-run test scenario without restarting Kafka.

---

