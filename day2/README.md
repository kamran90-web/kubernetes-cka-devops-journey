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



Commands to be used as imperative approach
# To create a pod
kubectl run <pod_name> --image=nginx --dry-run=client -o yaml

# To set the image of a container already running
kubectl set image pod/podName name_of_container=image_name




kubetl run nginx --image=nginx --dry-run=client -o yaml
kubectl create deploy --image=nginx my-deploy
kubectl expose deployment my-deploy --port 80
kubectl edit deployment my-deploy ----> kubectl replace --force --f<path-of-yaml>
kubectl scale deployment my-deploy nginx --replicas=5
kubectl set image deployment my-deploy nginx=nginx:1.18
kubectl create -f nginx.yaml
kubectl replace -f nginx.yaml
kubectl delete -f nginx.yaml


kubectl set resources deployment my-deploy --limits=cpu=200m,memory=512Mi --requests=cpu=100m,memory=250Mi

##create a svc named redis-src of type ClusterIp to epose pod redis on port 6379
kubectl expose pod redis --port=6379 --name redis-svc --dry-run=client -o yaml


#This command generates the YAML definition for a Kubernetes ClusterIP Service named redis that exposes port 6379.
kubectl create svc clusterip redis --tcp=6379:6379 --dry-run=client -o yaml


#Create a svc named nginx of type NodePort to expose pod nginx's port 80 on port 30080 on nodes
kubectl expose pod nginx --type=NodePort --port=80 --name=nginx-svc --dry-run=client -o yaml

#Note It will automatically use the pod's label on selector but cannot specify the node port. You have o generate a definition file and then add the nodeport in mnually before creating the svc in the pod 
