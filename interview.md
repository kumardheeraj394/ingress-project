---
🟢 Beginner Level
---
What is Kubernetes?

✅ Answer (English):
Kubernetes is an open-source container orchestration platform that automates the deployment, scaling, and management of containerized applications. It helps manage clusters of machines running containers, such as Docker, and makes it easier to run applications reliably at scale.

Kubernetes takes care of:

Automatically restarting failed containers

Load balancing traffic

Scaling apps based on demand

Managing configurations and secrets

Rolling out updates without downtime

It was originally developed by Google and is now maintained by the Cloud Native Computing Foundation (CNCF).


---
What is a Pod in Kubernetes?
---

A Pod is the smallest and simplest unit in the Kubernetes object model that you can create or deploy. It represents a single instance of a running process in a cluster.

Each Pod:

Can run one or more containers (usually one)

Shares the same network IP, storage, and configuration

Is ephemeral – if a Pod dies, Kubernetes creates a new one (with a new IP)

Is managed by higher-level controllers like Deployments, StatefulSets, etc.

---
What is the difference between a Pod and a Container?
---

What is a Deployment?

---
What is a Service in Kubernetes?
---

✅ Answer (English):
A Service in Kubernetes is an abstraction that defines a logical set of Pods and a policy to access them—usually through a stable network endpoint (IP and DNS name).

Since Pods in Kubernetes are ephemeral (they come and go), a Service provides a consistent way to communicate with them, even if the underlying Pods change.




---
What is kubectl? Can you list a few common commands?
---

✅ Answer (English):
kubectl is the command-line tool used to interact with a Kubernetes cluster. It allows you to deploy applications, inspect and manage resources, and view logs and cluster events.

You use kubectl to communicate with the Kubernetes API server.

🔧 Common kubectl Commands:

| Command                               | Description                          |
| ------------------------------------- | ------------------------------------ |
| `kubectl get pods`                    | List all pods                        |
| `kubectl get services`                | List all services                    |
| `kubectl get nodes`                   | Show all cluster nodes               |
| `kubectl describe pod <name>`         | Show detailed info about a pod       |
| `kubectl apply -f <file>.yaml`        | Apply configuration from a YAML file |
| `kubectl delete pod <name>`           | Delete a specific pod                |
| `kubectl logs <pod-name>`             | Show logs from a pod                 |
| `kubectl exec -it <pod-name> -- bash` | Access the shell inside a pod        |

---
What are the types of services in Kubernetes?
---
🔧 Types of Services:
ClusterIP (default) – Exposes the service within the cluster only.

NodePort – Exposes the service on each Node’s IP at a static port.

LoadBalancer – Exposes the service using a cloud provider’s external load balancer.

Headless Service – No ClusterIP; used for direct pod discovery, useful in StatefulSets.

---
What is a Namespace in Kubernetes?
---

✅ Answer (English):
A Namespace in Kubernetes is a way to divide cluster resources between multiple users or teams. It provides a virtual cluster inside a physical Kubernetes cluster, helping organize resources like Pods, Services, and Deployments.

Namespaces help in:

Resource isolation (different projects or teams)

Access control (using RBAC per namespace)

Avoiding name collisions (you can have same resource names in different namespaces)

Managing resource quotas and limits per namespace

By default, Kubernetes has a default namespace where resources are created if no namespace is specified.

---
How does Kubernetes perform load balancing?
---

✅ Answer (English):
Kubernetes provides load balancing mainly through its Service abstraction. Here’s how it works:

Internal Load Balancing:

When you create a Service of type ClusterIP (default), Kubernetes automatically load balances network traffic across all the Pods that match the Service’s selector inside the cluster.

This ensures requests are distributed evenly among healthy Pods.

External Load Balancing:

For services exposed outside the cluster, you can use Service types like NodePort or LoadBalancer.

LoadBalancer type integrates with cloud providers’ external load balancers (like AWS ELB, GCP LB) to distribute traffic to the cluster nodes and then to Pods.

DNS-based Load Balancing:

For Headless Services (no ClusterIP), Kubernetes creates DNS records that list all Pod IPs, allowing clients to implement their own load balancing logic.

---
What is kubelet and what does it do?
---
✅ Answer (English):
kubelet is an agent that runs on every worker node in a Kubernetes cluster. It is responsible for:

Communicating with the Kubernetes control plane (API server).

Receiving PodSpecs (pod definitions) from the control plane.

Ensuring that the containers described in the PodSpecs are running and healthy on the node.

Monitoring the containers and reporting their status back to the control plane.

Managing container lifecycle — starting, stopping, and restarting containers as needed.

In simple words, kubelet makes sure that the desired state of pods is maintained on each node

---
🟡 Intermediate Level
---
What is the difference between Deployment, StatefulSet, and DaemonSet?

How does a headless service work in Kubernetes?

How do you do rolling updates and rollbacks in Kubernetes?

What are ConfigMaps and Secrets? How are they used?

What is a ReplicaSet? How is it related to a Deployment?

What is the difference between a ClusterIP, NodePort, and LoadBalancer service?

What is a liveness probe and readiness probe?

What is the role of etcd in Kubernetes?

How does Kubernetes handle persistent storage (PVC, PV)?

How do you secure a Kubernetes cluster?

---
🔴 Advanced Level
---
How does the Kubernetes control plane work?

What happens when you run kubectl apply -f myapp.yaml?

How do you debug a pod stuck in CrashLoopBackOff?

How does Kubernetes handle networking between pods?

What is CNI (Container Network Interface)? Can you name some CNI plugins?

Explain the Horizontal Pod Autoscaler. How does it work?

How would you implement RBAC in Kubernetes?

Explain the architecture of Kubernetes.

How does service discovery work in Kubernetes?

What tools do you use to monitor a Kubernetes cluster? (e.g., Prometheus, Grafana, etc.)
---
