# Day 1 Task — Imperative vs Declarative Kubernetes

## Objective
Learn the difference between **imperative** and **declarative** Kubernetes workflows by creating a Pod using both approaches.

---

## 1️⃣ Background

- **Imperative**: You tell Kubernetes *exactly what to do* using commands.
  - Quick and direct.
  - Example: `kubectl create pod ...`
- **Declarative**: You define the desired state in a YAML file, and Kubernetes ensures the cluster matches it.
  - More maintainable and versionable.
  - Example: `kubectl apply -f pod.yaml`

---

## 2️⃣ Imperative Task

1. Create a pod named `nginx-imperative` using the `nginx:latest` image:

```bash
kubectl run nginx-imperative --image=nginx:latest --restart=Never


Verify the pod is running:
kubectl get pods
kubectl describe pod nginx-imperative

# Declarative Task

Create a file named nginx-declarative.yaml with the following content:
apiVersion: v1
kind: Pod
metadata:
  name: nginx-declarative
spec:
  containers:
    - name: nginx
      image: nginx:latest
      ports:
        - containerPort: 80


Apply the YAML to create the pod:
kubectl apply -f nginx-declarative.yaml

Verify the pod is running:
kubectl get pods
kubectl describe pod nginx-declarative

Key Takeaways

Imperative is good for quick experiments but not version-controlled.

Declarative is preferred for production; YAML files can be stored in Git for tracking changes.

Both methods achieve the same goal: creating resources in Kubernetes.
