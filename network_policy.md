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
                               | Ingress (port 80)
                               v
                     +--------------------+
                     |   Frontend Pod     |
                     |   (Port: 80)       |
                     +--------------------+
                               |
                               | Egress
                               v
                     +--------------------+
                     |   Backend Pod      |
                     |   (Port: 3000)     |
                     +--------------------+
                               |
                               | Egress
                               v
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
