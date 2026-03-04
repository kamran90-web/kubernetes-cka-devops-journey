# Day 1 – Imperative vs Declarative Approach in Kubernetes

Welcome to **Day 1** of my **40 Days of Kubernetes** journey! 🚀  

Today we’re learning one of the most foundational concepts in Kubernetes:  

**Imperative vs Declarative Approaches**  

Understanding this is essential for:  

- Passing the **CKA exam**  
- Working in production-ready Kubernetes environments  
- Becoming a confident DevOps engineer  

---

## 🧠 What is Kubernetes?

Kubernetes (K8s) is a **container orchestration platform** that automates:

- Deployment of containerized applications  
- Scaling and load balancing  
- Self-healing (auto-restarting containers)  
- Managing secrets and configuration  

It is maintained by the **Cloud Native Computing Foundation (CNCF)**.

> Simply put: Kubernetes makes sure your containers run the way you want, automatically.

---

## 🔹 Imperative Approach

Imperative = **command-driven**.  
You tell Kubernetes **step by step** what to do.

- Fast for learning and testing  
- Useful during **CKA exam**  
- Not ideal for production  

### Example 1: Create a Pod

```bash
kubectl run nginx-pod --image=nginx
kubectl get pods

Explanation:

## kubectl run → creates a pod

## nginx-pod → pod name

## --image=nginx → container image



## Check Details:
kubectl describe pod nginx-pod
kubectl logs nginx-pod





## Create and Scale a Deployment

kubectl create deployment nginx-deploy --image=nginx
kubectl scale deployment nginx-deploy --replicas=3
kubectl get deployments
kubectl get pods

## Notes:

1. Imperative changes happen immediately

2. There is no YAML file

3. Changes are not version-controlled.


Pros

1. Quick and simple
2. Useful for debugging
3. Time-saving for CKA exam


Cons

1. Hard to track changes
2. Not reproducible
3.Not suitable for production

####Declarative Approach###

Declarative = YAML-driven.
You define desired state, and Kubernetes ensures the system matches it.

1. Version-controlled
2. Production-ready
3. Supports CI/CD automation.

Example Deployment YAML

Create deployment.yaml:
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deploy
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx

Apply It:
kubectl apply -f deployment.yaml
kubectl get deployments
kubectl get pods

Explanation:

apiVersion: apps/v1 → version of Deployment API
kind: Deployment → type of Kubernetes resource
replicas: 2 → desired number of pods
template → defines pod spec, including container image


✅ Pros

Version-controlled (Git friendly)
Reproducible and consistent
Automatically maintains desired state
Production-ready

❌ Cons

Requires YAML knowledge
Slightly more initial setup
