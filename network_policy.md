# Ingress - Egress in Kubernetes

![Ingress-Egress Diagram](80af5020-533d-4313-a3a4-3c2dc7264db4.png)

## Overview

In Kubernetes, **Ingress** and **Egress** describe the direction of network traffic:

- **Ingress**: Incoming traffic to a Pod or service.
- **Egress**: Outgoing traffic from a Pod or service.

---

## Diagram Explanation

The diagram depicts the network flow between Pods in a Kubernetes cluster:

1. **User to Frontend Pod**
   - **Ingress** occurs at port **80**.
   - The external user sends HTTP traffic to the **Frontend Pod**.

2. **Frontend Pod to Backend Pod**
   - The **Frontend Pod** sends **Egress** traffic to the **Backend Pod**.
   - The **Backend Pod** receives this as **Ingress** traffic on port **3000**.

3. **Backend Pod to Database Pod**
   - The **Backend Pod** sends **Egress** traffic to a **Database Pod**.
   - The **Database Pod** receives this as **Ingress** traffic on port **3306**.

---

## Key Concepts

| Term    | Definition                                 |
|---------|--------------------------------------------|
| Ingress | Incoming traffic to a Pod or service        |
| Egress  | Outgoing traffic from a Pod or service      |

---

## Notes

- Managing Ingress and Egress rules is essential for security and connectivity.
- Kubernetes Network Policies can be used to control allowed Ingress and Egress.
