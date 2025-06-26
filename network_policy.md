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
---

✅ To Use NetworkPolicy Effectively
Make sure your cluster is using one of these:

 Calico

 Cilium

 Antrea

 Kube-router

 Romana

 Weave Net

📌 Summary
Kubernetes defines NetworkPolicy.

Your CNI plugin must support it to make it work.

Choose from supported plugins like Calico, Cilium, Weave Net, etc.

Ensure proper CNI installation and configuration before using NetworkPolicy in production clusters.
---
