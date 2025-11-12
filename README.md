# Lab 7 - CST8915 Full-stack Cloud-native Development: Introduction to Kubernetes Basics

**Student:** Ken Biju Jacob
**Student ID:** 041171140

## 🧩 RabbitMQ Configuration Analysis

### 1️⃣ Is RabbitMQ Stateless or Stateful?
RabbitMQ is a **stateful application** because it stores:
- Message queues and routing metadata  
- User credentials and exchange definitions  
- Acknowledgement and delivery states  

All of these are persisted to `/var/lib/rabbitmq` inside the container.

---

### 2️⃣ Implications of Running Without Persistent Storage
In the provided YAML, RabbitMQ runs as a simple **Deployment** without a PersistentVolumeClaim (PVC).  
Therefore:
- Data is kept only in the container’s ephemeral filesystem.  
- When the pod is deleted or rescheduled, all queues and messages are lost.  
- Order and Product services lose their connection and pending messages.  
- The system becomes unreliable for any real use.

---

### 3️⃣ What Happens When the RabbitMQ Pod Is Deleted or Restarted
- A new container is created with a fresh, empty `/var/lib/rabbitmq`.  
- All previous queues, messages, and credentials disappear.  
- Applications must re-create queues and connections.  

---

### 4️⃣ Potential Solutions (Research Based)

| Problem | Recommended Fix | Description |
|-----------|----------------|--------------|
| Ephemeral storage | 🗃️ **Add PVC** | Mount a PersistentVolumeClaim to `/var/lib/rabbitmq` so data survives restarts. |
| Changing pod names and IPs | 🧩 **Use StatefulSet** | Provides stable network IDs and persistent volumes per replica. |
| Plain-text credentials | 🔐 **Use Secrets** | Store RabbitMQ username/password securely via Kubernetes Secrets. |
| No health checks | 🩺 **Add probes** | Add liveness and readiness probes for automatic restarts and traffic gating. |
| Manual scaling and failover | ☁️ **Managed Service (e.g., Azure Service Bus)** | Offload durability and availability to a fully managed broker. |

---

### 5️⃣ Does Azure Service Bus Solve These Issues?
✅ **Yes.**  
Azure Service Bus is a fully managed, durable message broker that provides:
- Automatic persistence and replication  
- High availability across zones  
- Built-in dead-letter queues and monitoring  
- No need for PVCs or cluster management  

Using Azure Service Bus removes the operational risks and message-loss problems seen with the stateless RabbitMQ deployment.

---
