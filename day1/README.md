# Day 1: Kubernetes Architecture – Control Plane & Nodes

Welcome to Day 1 of the "Kubernetes Zero to Hero" series! 🚀  
Before we start creating Pods or Deployments, it’s crucial to understand **how Kubernetes works under the hood**.

---

## **1. Kubernetes Cluster Overview**

A Kubernetes cluster consists of **two main parts**:

1. **Control Plane (Master components)** – The “brain” of the cluster  
2. **Worker Nodes** – The “muscles” that run workloads (Pods/Containers)

**High-Level Flow:**
User CLI / Dashboard
|
kube-apiserver
|
Controller Manager <--> etcd <--> Scheduler
|
Worker Nodes (kubelet + containers)



---

## **2. Control Plane Components (Master Node)**

The Control Plane **maintains the desired state** of the cluster and manages scheduling, scaling, and cluster-wide decisions.  

### **a) kube-apiserver**
- The **front-end of the Control Plane**  
- Handles all REST API requests (`kubectl`, UI, CI/CD tools)  
- Authenticates & validates requests  
- Writes the cluster state to **etcd**  

> Practical tip: In CKA, if pods aren’t scheduling, check API server connectivity.

---

### **b) etcd**
- **Distributed key-value store** for Kubernetes state  
- Stores **all cluster configuration and metadata**, e.g., Pods, Services, Secrets  
- Must be backed up regularly in production  

> Example: Deleting etcd data can destroy your cluster—CKA exam may ask about etcd backup/restore.

---

### **c) kube-scheduler**
- Assigns Pods to Worker Nodes  
- Decides **where a Pod should run** based on:
  - Node resources (CPU/memory)
  - Labels & node affinity
  - Taints & tolerations  
- Works in the background and continuously rebalances Pods if needed

---

### **d) kube-controller-manager**
- Runs **cluster controllers** as separate processes:
  - **Node Controller:** Tracks node health
  - **Replication Controller:** Ensures the right number of replicas
  - **Endpoint Controller:** Populates Service endpoints
  - **Job Controller:** Monitors completion of batch jobs
- Ensures **desired state matches current state**

---

### **e) cloud-controller-manager (optional)**
- Integrates with **cloud provider APIs**  
- Manages resources like Load Balancers, Node lifecycle in cloud  
- Only relevant for cloud-hosted clusters (AWS, GCP, Azure)

---

## **3. Worker Node Components**

Worker nodes actually run your workloads. Each node has:

### **a) kubelet**
- Agent that communicates with the **Control Plane**  
- Watches for **PodSpecs** and ensures containers are running  
- Sends node & pod status back to API server

### **b) kube-proxy**
- Handles **network routing for Services**  
- Implements **ClusterIP, NodePort, and LoadBalancer** networking  
- Uses **iptables or IPVS** rules under the hood

### **c) Container Runtime**
- Runs containers (Docker, containerd, CRI-O)  
- Pulls container images and starts the container as defined in the PodSpec

---

## **4. How the Control Plane and Nodes Interact**

1. **User submits YAML** with `kubectl apply -f pod.yaml`  
2. **API server** receives request and validates it  
3. **Scheduler** selects the best node based on resources & constraints  
4. **kubelet** on the selected node creates and monitors containers  
5. **Controller manager** ensures desired state is maintained (restarts pods if needed)  
6. **etcd** stores the authoritative state for recovery and audit

---

## **5. Diagram (Detailed View)**


      +--------------------+
      |     kubectl / UI   |
      +---------+----------+
                |
        +-------v-------+
        | kube-apiserver|
        +-------+-------+
                |


+----------------+----------------+
| Controller Manager |
| - Node Controller |
| - Replication Controller |
| - Job Controller |
+----------------------------------+
|
+------v------+
| Scheduler |
+------^------+
|
+------v------+
| etcd |
+------+------+
|
Worker Nodes (Multiple)
+----------------+----------------+
| kubelet | kube-proxy | Container Runtime |
| Pods/Containers running your apps |
+---------------------------------------+



---

## **6. Key Takeaways**

- **Control Plane:** The “brain” – schedules, manages, and maintains cluster state  
- **Worker Nodes:** The “muscles” – run actual workloads (Pods/Containers)  
- **Communication:** kubelet ↔ kube-apiserver ensures the desired state is met  
- **etcd:** Single source of truth; critical for backup/restore  

> 💡 **CKA Tip:** Most troubleshooting questions in the exam revolve around **control plane components and node health**. Knowing this architecture is essential.

---

📌 **Next Step (Day 2):** Declarative vs Imperative Approach – How to manage Kubernetes resources.
