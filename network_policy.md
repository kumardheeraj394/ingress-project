# Kubernetes Ingress - Egress Overview

This document explains the concept of **Ingress** and **Egress** traffic in a Kubernetes cluster using a textual (ASCII) diagram.

---

## 📘 Terminology

- **Ingress**: Incoming network traffic **to a Pod**.
- **Egress**: Outgoing network traffic **from a Pod**.

---

## 📊 ASCII Diagram

                     +--------------------+
                     |      User          |
                     +--------------------+
                               |
                               | 
                               v Ingress (port 80)
                     +--------------------+
                     |   Frontend Pod     |
                     |   (Port: 80)       |
                     +--------------------+
                               | Egress
                               | 
                               v Ingress (port 3000)
                     +--------------------+
                     |   Backend Pod      |
                     |   (Port: 3000)     |
                     +--------------------+
                               | Egress
                               | 
                               v Ingress (port 3306)
                     +--------------------+
                     |   DB Pod           |
                     |   (Port: 3306)     |
                     +--------------------+



---

## 🔁 Traffic Flow Explained

1. **User to Frontend Pod**
   - The user sends a request to the cluster.
   - This traffic reaches the **Frontend Pod** on port **80** (Ingress).

2. **Frontend Pod to Backend Pod**
   - The frontend service makes a request to the **Backend Pod** on port **3000**.
   - This is **Egress** from the frontend and **Ingress** to the backend.

3. **Backend Pod to Database Pod**
   - The backend pod queries the **Database Pod** on port **3306**.
   - This is another **Egress** (from backend) and **Ingress** (to database).

---

## ✅ Summary

| Pod         | Ingress Port | Egress Target         |
|-------------|---------------|------------------------|
| Frontend    | 80            | Backend (port 3000)    |
| Backend     | 3000          | Database (port 3306)   |
| Database    | 3306          | None (acts as endpoint)|

---

## 🔐 Security Consideration

Use Kubernetes **NetworkPolicies** to define what ingress and egress traffic is allowed for each pod.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-ingress
spec:
  podSelector:
    matchLabels:
      app: frontend
  ingress:
    - from:
        - podSelector:
            matchLabels:
              role: user
```
---

# Kubernetes Network Policy and CNI Plugins

To **deploy and enforce** a Kubernetes `NetworkPolicy`, your cluster **must use a CNI (Container Network Interface)** plugin that supports it.

---

## ✅ Supported CNI Plugins for Network Policies

| Category       | Network Plugins That Support NetworkPolicy |
|----------------|---------------------------------------------|
| **Antrea**     | - Calico<br> - Cilium                      |
| **Kube-router**| - Romana<br> - Weave Net                   |

> These CNIs both enable pod-to-pod communication **and** enforce network isolation via Network Policies.

---

## 📘 Why This Matters

Kubernetes provides the `NetworkPolicy` resource to define rules for **pod-level traffic control** (Ingress and Egress), but it does **not enforce** the rules.

👉 The **CNI plugin** is responsible for the **actual enforcement**.

---

## 🚫 Not All CNIs Support NetworkPolicy

For example:

- **Flannel** (default in kubeadm clusters) does **not** support NetworkPolicy by default.
- Using unsupported CNIs means your policy definitions will be ignored.

---

## 🔐 Sample Network Policy (Only Effective With Supporting CNI)

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend
spec:
  podSelector:
    matchLabels:
      app: frontend
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: backend
```
---

✅ To Use NetworkPolicy Effectively
Make sure your cluster is using one of these:
# Kubernetes Network Policy - CNI Plugin Compatibility

Kubernetes **Network Policies** allow you to control traffic flow between pods, but they require a **CNI plugin that supports enforcement**.

---

## ✅ Supported CNI Plugins

These plugins **support NetworkPolicy enforcement** and can be safely used to implement ingress/egress rules.

| CNI Plugin   | NetworkPolicy Support  | Notes                                           |
|--------------|------------------------|-------------------------------------------------|
| Calico       | ✅ Yes                 | Most popular, highly configurable               |
| Cilium       | ✅ Yes                 | eBPF-based, high performance                    |
| Antrea       | ✅ Yes                 | Built on Open vSwitch, supports advanced policy |
| Kube-router  | ✅ Yes                 | Lightweight router and network policy engine    |
| Romana       | ✅ Yes                 | Simple L3 network policy plugin                 |
| Weave Net    | ✅ Yes                 | Easy to set up, supports encryption             |
| Tigera       | ✅ Yes                 | Enterprise version of Calico                    |
| OpenShift SDN| ✅ Yes                 | Default CNI in OpenShift clusters               |

---

## ❌ Unsupported or Partially Supported CNI Plugins

These plugins **do not support** NetworkPolicy out-of-the-box or have limited support.

| CNI Plugin   | NetworkPolicy Support  | Notes                                                  |
|--------------|------------------------|--------------------------------------------------------|
| Flannel      | ❌ No (default mode)   | Requires custom setup with `Canal` to support policies |
| AWS VPC CNI  | ⚠️ Partial             | Ingress supported; Egress not enforced natively        |
| Azure CNI    | ⚠️ Partial             | Depends on Azure configuration and policy engine       |
| GKE (legacy) | ⚠️ Partial             | Varies by mode (VPC-native required)                   |
| Docker (bridge)| ❌ No                | Not meant for production; no policy enforcement        |
| Host-GW (raw bridge) | ❌ No         | Bare routing with no isolation support                  |

---

## 🔎 How to Check What CNI is Installed

Run this command on your Kubernetes master node:

```bash
kubectl get pods -n kube-system -o wide
```


📌 Summary
Kubernetes defines NetworkPolicy.

Your CNI plugin must support it to make it work.

Choose from supported plugins like Calico, Cilium, Weave Net, etc.

Ensure proper CNI installation and configuration before using NetworkPolicy in production clusters.
---

✅ Recommendation
For production clusters with security requirements:

Use Calico or Cilium for rich policy support.

Avoid relying on Flannel unless paired with Canal (Calico + Flannel).
---
---

Case 1: Ingress NetworkPolicy

             +------------------+
             |   Database Pod   |
             |   role: db       |
             +--------+---------+
                      ^
                      |  Allowed (port 6379)
                      |
       +--------------+--------------+
       |              |              |
+------------+  +------------+  +------------+
| Backend Pod|  | Backend Pod|  | Backend Pod|
| role:backend| | role:backend| | role:backend|
+-------------+ +-------------+ +-------------+

        Other Pods (e.g., Frontend or no label)
        +------------------+
        |    Blocked Pod   |
        |   (not backend)  |
        +------------------+
                 X
               Denied
---
# Kubernetes Network Policy - Case 1

This case demonstrates how to restrict access to a **Database Pod** using Kubernetes NetworkPolicy. Only pods with a label `role: backend` can connect to it.

---

## 📘 NetworkPolicy YAML

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: test-network-policy
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
    - Ingress
    - Egress
  ingress:
    - from:
        - podSelector:
            matchLabels:
              role: backend
      ports:
        - protocol: TCP
          port: 6379
```
---
🔐 Behavior Summary
Applies to: All pods labeled role: db (i.e., the Database Pod).

Allows Ingress From: Pods labeled role: backend.

Port Allowed: 6379/TCP.

All Other Ingress Traffic: Denied by default
---

             +------------------+
             |   Database Pod   |
             |   role: db       |
             +--------+---------+
                      ^
                      |  Allowed (port 6379)
                      |
       +--------------+--------------+
       |              |              |
+------------+  +------------+  +------------+
| Backend Pod|  | Backend Pod|  | Backend Pod|
| role:backend| | role:backend| | role:backend|
+-------------+ +-------------+ +-------------+

        Other Pods (e.g., Frontend or no label)
        +------------------+
        |    Blocked Pod   |
        |   (not backend)  |
        +------------------+
                 X
               Denied
---
| Source Pod Label | Can Access Database? | Why                        |
| ---------------- | -------------------- | -------------------------- |
| `role: backend`  | ✅ Yes                | Matches NetworkPolicy rule |
| `role: frontend` | ❌ No                 | Not matched in policy      |
| No label         | ❌ No                 | No match = traffic blocked |
---

✅ Notes
This policy controls Ingress to the Database Pod.

All traffic not explicitly allowed is denied.

If you need Egress restrictions, you must define them separately.

---

✅ Explanation of Case 2:
This case demonstrates how to allow traffic to a Database Pod (role: db) only from a specific namespace and pod label combination.

🔐 What the YAML Policy Does:
---
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: test-network-policy
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
    - Ingress
    - Egress
  ingress:
    - from:
        - podSelector:
            matchLabels:
              role: backend
          namespaceSelector:
            matchLabels:
              ns: prod
```
---
Applies to pods with label role: db

Allows Ingress traffic only from:

Pods with label role: backend

Located in the namespace labeled ns: prod

This provides namespace-level and pod-level isolation in one rule.
---

Case 2: Ingress Policy with Namespace + PodSelector

               +------------------+
               |   Database Pod   |
               |   role: db       |
               +--------+---------+
                        ^
                        |  Allowed Ingress
                        |
         From Namespace: ns=prod
         With Label: role=backend
                        |
               +------------------+
               |  Backend Pod     |
               |  role: backend   |
               |  ns: prod        |
               +------------------+

        Other Namespaces or Wrong Labels → Denied
---

🔐 Policy Behavior
Selected Pod: role: db (i.e., Database Pod)

Ingress allowed only from:

Pods with label: role: backend

In namespaces labeled: ns: prod

All other traffic is denied by default.

---

| Namespace Label | Pod Label        | Can Access `role=db` Pod? | Reason                        |
| --------------- | ---------------- | ------------------------- | ----------------------------- |
| `ns: prod`      | `role: backend`  | ✅ Yes                     | Matches both selectors        |
| `ns: prod`      | `role: frontend` | ❌ No                      | Pod label doesn't match       |
| `ns: dev`       | `role: backend`  | ❌ No                      | Namespace label doesn't match |
---

✅ Ensure your CNI plugin (e.g., Calico, Cilium) supports both podSelector and namespaceSelector in NetworkPolicies.
