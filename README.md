# Log Ingestor: Kubernetes to Kafka with Fluent Bit

This project sets up a pipeline to collect logs from a Kubernetes cluster using Fluent Bit and stream them to a Kafka topic for real-time processing.

---

## 🔧 Components Used

- **Fluent Bit**: Lightweight log forwarder deployed as a DaemonSet.
- **Kafka**: Message queue for storing and streaming logs.
- **Docker Compose**: Runs Kafka and Zookeeper locally.
- **Kubernetes (Minikube)**: Local Kubernetes cluster.

---

## 🗂 Directory Structure

```plaintext
fluent-bit/
├── configmap.yaml         # Fluent Bit configuration (filters, input, output)
├── daemonset.yaml         # DaemonSet definition for Fluent Bit
├── serviceaccount.yaml    # ServiceAccount for RBAC

kafka/
├── docker-compose.yml     # Runs Kafka and Zookeeper via Docker Compose
```

---

## 🚀 Quick Start
1. Start Kafka (Docker Compose)
```
cd kafka
HOST_IP=<your-host-ip> docker-compose up -d
```
Ensure KAFKA_ADVERTISED_LISTENERS in docker-compose.yml uses your actual host IP.

2. Apply Fluent Bit setup to Kubernetes
```
kubectl apply -f fluent-bit/serviceaccount.yaml
kubectl apply -f fluent-bit/configmap.yaml
kubectl apply -f fluent-bit/daemonset.yaml
```

---

3. Verify Log Ingestion
```
kubectl run logger --image=busybox --restart=Never -- sh -c "while true; do echo hello from k8s; sleep 5; done"
docker exec -it kafka kafka-console-consumer.sh \
  --bootstrap-server <host-ip>:9092 \
  --topic k8s-logs
```

---

## 🧪 Troubleshooting
- No logs in Kafka?
  - Ensure Fluent Bit pods are running: kubectl get pods -n kube-system -l k8s-app=fluent-bit
  - Check logs: kubectl logs -n kube-system -l k8s-app=fluent-bit
  - Verify Kafka IP in fluent-bit.conf matches your machine IP.
 
---
 
## 📌 Notes
- Works best when Minikube uses the Docker driver.
- Kafka must be reachable via the same host IP used in KAFKA_ADVERTISED_LISTENERS.
- Fluent Bit enriches logs using the Kubernetes filter.

---
