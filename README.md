---

## 1️⃣ Namespaces

Namespaces provide **logical separation** of resources within a cluster.

### Create a Namespace
```bash
kubectl create namespace dev
kubectl get ns
Use a Namespace
kubectl get pods -n dev
kubectl apply -f pod.yaml -n dev

Notes:

ResourceQuotas and LimitRanges are namespace-scoped.
Helps isolate teams/projects within the same cluster.
2️⃣ ResourceQuota

ResourceQuota enforces limits on total resources per namespace.

Example: ResourceQuota YAML
apiVersion: v1
kind: ResourceQuota
metadata:
  name: dev-quota
  namespace: dev
spec:
  hard:
    pods: "4"
    requests.cpu: "2"
    requests.memory: "2Gi"
    limits.cpu: "4"
    limits.memory: "4Gi"
Apply ResourceQuota
kubectl apply -f resourcequota.yaml
kubectl get rq -n dev
kubectl describe rq dev-quota -n dev

Key points:

Enforces max resources in a namespace.
Requests and limits count toward quota.
Pods exceeding the quota cannot be created.
3️⃣ LimitRange

LimitRange sets per-Pod or per-Container min/max/default resources.

Example: LimitRange YAML
apiVersion: v1
kind: LimitRange
metadata:
  name: my-limit
  namespace: dev
spec:
  limits:
  - type: Container
    min:
      cpu: "2"
      memory: "1Gi"
    max:
      cpu: "5"
      memory: "5Gi"
    defaultRequest:
      cpu: "3"
      memory: "2Gi"
    default:
      cpu: "4"
      memory: "4Gi"
Apply LimitRange
kubectl apply -f limitrange.yaml
kubectl get limitrange -n dev
kubectl describe limitrange my-limit -n dev

Key points:

type is mandatory (Container or Pod).
min/max → absolute boundaries; creation fails if exceeded.
defaultRequest/default → automatically applied if Pod/container does not specify resources.
Together with ResourceQuota, ensures controlled resource usage per namespace.
4️⃣ Example workflow
# 1. Create namespace
kubectl create ns dev

# 2. Apply ResourceQuota
kubectl apply -f resourcequota.yaml

# 3. Apply LimitRange
kubectl apply -f limitrange.yaml

# 4. Deploy a Pod without specifying resources
kubectl apply -f pod.yaml -n dev

# 5. Verify applied requests/limits
kubectl describe pod <pod-name> -n dev
kubectl describe rq -n dev
kubectl describe limitrange -n dev

Result: Pod will automatically inherit defaultRequest and default limits, and namespace-wide quotas will be enforced.

5️⃣ Notes for CKA
ResourceQuota: namespace-level total limits.
LimitRange: per-Pod/container min/max/default.
Namespaces: provide logical separation; quotas and limits are namespace-scoped.
Pods without requests/limits inherit defaults from LimitRange.
QoS class depends on requests vs limits:
Guaranteed: requests = limits
Burstable: requests < limits
BestEffort: no requests/limits




1️⃣ What is a LimitRange?

In Kubernetes:

A LimitRange is a namespace-scoped object that enforces resource constraints on Pods and Containers.

It helps ensure:

Minimum and maximum resources per Pod or Container
Default resources if not specified
Consistent resource usage across a namespace
Helps maintain QoS (Quality of Service) classifications
2️⃣ Why LimitRange is needed

Without a LimitRange:

Users can deploy Pods with too few or too many resources, affecting other workloads.
Requests and limits may be inconsistent → cluster nodes may be overcommitted.
QoS class may not behave as expected.

With LimitRange:

You can set defaults so Pods automatically get requests/limits.
You can enforce minimum and maximum CPU/memory.
Helps control bursting and resource usage in shared namespaces.
3️⃣ Fields in LimitRange

A typical LimitRange YAML looks like this:

apiVersion: v1
kind: LimitRange
metadata:
  name: my-limit
  namespace: dev
spec:
  limits:
  - type: Container               # Must be set: Container or Pod
    min:
      cpu: "100m"
      memory: "128Mi"
    max:
      cpu: "2"
      memory: "1Gi"
    defaultRequest:
      cpu: "200m"
      memory: "256Mi"
    default:
      cpu: "500m"
      memory: "512Mi"
Explanation of fields
Field	Meaning
type	Applies to Container (each container) or Pod (sum of containers)
min	Minimum resource a Pod/container can request. Creation fails if below this.
max	Maximum resource a Pod/container can request. Creation fails if above this.
defaultRequest	Request automatically applied if user does not specify a request
default	Limit automatically applied if user does not specify a limit
maxLimitRequestRatio	Optional. Maximum ratio of limit / request.
4️⃣ Types of LimitRange
Container type
Applies to each container individually
Most common
Example: Each container must request at least 100m CPU, no more than 2 CPU
Pod type
Applies to the total resources of all containers in a Pod
Example: Total CPU of Pod cannot exceed 4 CPU
5️⃣ QoS and LimitRange

Kubernetes assigns a QoS class based on requests vs limits:

Scenario	QoS Class
requests = limits	Guaranteed
requests < limits	Burstable
no requests/limits	BestEffort

LimitRange ensures requests and limits exist, which helps assign the correct QoS automatically.

6️⃣ Example workflow
Create a namespace
kubectl create ns dev
Apply LimitRange
kubectl apply -f limitrange.yaml
Deploy Pod without specifying resources
apiVersion: v1
kind: Pod
metadata:
  name: test-pod
  namespace: dev
spec:
  containers:
  - name: nginx
    image: nginx
Check applied resources
kubectl describe pod test-pod -n dev
Requests and limits are automatically filled with defaultRequest and default values from LimitRange.
7️⃣ Important Notes
type must be set; otherwise, Kubernetes rejects the LimitRange.
LimitRange is namespace-scoped, so you need one per namespace.
It complements ResourceQuota for robust resource management.
Pods exceeding min/max values cannot be created.
Helps prevent noisy neighbors in shared clusters.
8️⃣ Example Table of Effects
Pod Request / Limit	Result with LimitRange
No request, no limit	defaultRequest / default applied
request < min	Creation fails
request > max	Creation fails
request between min and max, limit unspecified	Limit = default
request = limit = min/max	Allowed, QoS Guaranteed
9️⃣ Summary
LimitRange → enforce min/max/default per container or pod.
Namespace-scoped → each namespace can have its own rules.
Automatically fills requests/limits → ensures predictable QoS.
Always use with ResourceQuota in production for proper resource control.
