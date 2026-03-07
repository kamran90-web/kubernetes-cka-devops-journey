# Day 3: Understanding Pods – The Smallest Deployable Unit in Kubernetes

Welcome to **Day 3 of the Kubernetes Zero to Hero series** 🚀

In Kubernetes, applications do not run directly as containers.  
Instead, they run inside **Pods**, which are the smallest deployable unit in Kubernetes.

Understanding Pods is essential because **every Kubernetes workload eventually runs inside a Pod**.

---

# What is a Pod?

A **Pod** is the smallest object that you can create in Kubernetes.

A Pod can contain:

- **Single container** (most common)
- **Multiple containers** that are tightly coupled

All containers inside a Pod share:

- Network (same IP address)
- Storage volumes
- Lifecycle

Example structure:

Pod
├── Container (nginx)
└── Container (sidecar logger)


---

# Key Characteristics of Pods

## 1. Shared Network

All containers inside a Pod share the **same IP address and port space**.

Example:

Container A → localhost:8080
Container B → localhost:8080


This allows containers to communicate with each other easily.

---

## 2. Shared Storage

Containers inside a Pod can share **volumes**.

Example use case:


Pod
├── App container
└── Logging container
|
Shared Volume

The application writes logs and the logging container processes them.

---

## 3. Ephemeral Nature

Pods are **temporary**.

If a Pod crashes:

- Kubernetes does **not restart the same Pod**
- Controllers like **Deployments create a new Pod**

---

# Creating a Pod Using YAML

Create a file:
nginx-pod.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
      - containerPort: 80



Create the Pod

Apply the configuration:
kubectl apply -f nginx-pod.yaml

Inspect the Pod

To see detailed information:
kubectl describe pod nginx-pod

This command shows:

Node where the Pod is running
Container details
Events
Networking info

View Pod Logs
To see container logs:
kubectl logs nginx-pod

Execute Commands Inside the Pod
You can run commands inside the container:
kubectl exec -it nginx-pod -- /bin/bash

Check Pod IP

Each Pod gets its own IP address.
kubectl get pod -o wide

Key Takeaways

Pods are the smallest deployable unit in Kubernetes
A Pod can run one or multiple containers
Containers in a Pod share network and storage
Pods are ephemeral and replaceable
In production, Pods are usually managed by Deployments


Next in the Series

Day 4: Deployments & ReplicaSets – Managing and Scaling Pods

We will learn:
Why Pods should not be used directly
How Deployments manage Pods
Rolling updates and rollbacks 
