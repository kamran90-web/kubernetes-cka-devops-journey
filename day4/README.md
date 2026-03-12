Day 4 – Understanding Kubernetes Services

In the previous days of this CKA preparation series, we explored Kubernetes basics like Pods and deployments. Today, we will focus on Kubernetes Services, one of the most important concepts for enabling communication inside and outside the cluster.

Pods in Kubernetes are ephemeral. Their IP addresses change whenever they restart or get recreated. This makes it difficult for other applications to reliably communicate with them.
This is where Services come into play.

What is a Kubernetes Service?

A Service in Kubernetes is an abstraction that exposes a set of Pods as a stable network endpoint.

Instead of connecting directly to Pods, applications communicate through a Service, which maintains a consistent IP address and DNS name.

Key benefits of Services:

Stable network identity for Pods

Load balancing across multiple Pods

Service discovery inside the cluster

Ability to expose applications outside the cluster

How Services Work

Services use labels and selectors to identify which Pods belong to them.

Example flow:

Pods are created with specific labels.

A Service is defined with a selector that matches those labels.

The Service routes traffic to all matching Pods.

Example Pod label:

labels:
  app: nginx

Service selector:

selector:
  app: nginx

Kubernetes automatically sends traffic to any Pod that matches the selector.

Types of Kubernetes Services

Kubernetes provides several Service types depending on how you want to expose your application.

1. ClusterIP (Default)

ClusterIP exposes the Service only inside the cluster.

Use case:

Communication between internal services.

Example:

apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: ClusterIP
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80

Access example inside cluster:

http://nginx-service
2. NodePort

NodePort exposes the Service on a static port on every node.

External users can access it using:

<NodeIP>:<NodePort>

Example:

apiVersion: v1
kind: Service
metadata:
  name: nginx-nodeport
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30007

Access example:

http://192.168.1.10:30007
3. LoadBalancer

LoadBalancer exposes the application using a cloud provider load balancer.

Common in cloud environments like AWS, Azure, and GCP.

Example:

apiVersion: v1
kind: Service
metadata:
  name: nginx-loadbalancer
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80

The cloud provider automatically provisions an external IP.

4. ExternalName

ExternalName maps a Service to an external DNS name.

Example:

apiVersion: v1
kind: Service
metadata:
  name: external-db
spec:
  type: ExternalName
  externalName: database.example.com

This is useful when connecting Kubernetes applications to external services.

Important Commands for CKA

Create a Service:

kubectl expose pod nginx --port=80 --name=nginx-service

View Services:

kubectl get svc

Describe a Service:

kubectl describe svc nginx-service

Delete a Service:

kubectl delete svc nginx-service
Key CKA Exam Tips

Remember that ClusterIP is the default Service type.

Services use selectors to match Pods.

NodePort range is usually 30000–32767.

Services provide load balancing across Pods automatically.
