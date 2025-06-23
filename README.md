# ingress-project

This project sets up the NGINX Ingress Controller on a Kubernetes cluster using a manifest hosted in a public GitHub repository.

## 🔧 Installation

Apply the Ingress controller deployment using `kubectl`:

```bash
kubectl apply -f https://raw.githubusercontent.com/kumardheeraj394/ingress-project/refs/heads/main/ingress-deploy.yaml
```
---
```bash
kubectl get ns
```
---
```bash
NAME              STATUS   AGE
argocd            Active   27d
default           Active   28d
dev               Active   6d22h
ingress-nginx     Active   27d
```
---
# Kubernetes Ingress-NGINX Deployment Status

This document provides an overview of the current state of the NGINX Ingress Controller deployment in the `ingress-nginx` namespace.

---

## 📦 Resources Overview

### 🔹 Pods

```bash
kubectl get pods -n ingress-nginx
---
| Name                                      | Status           | Restarts    | Age |
| ----------------------------------------- | ---------------- | ----------- | --- |
| ingress-nginx-admission-create-g5w9x      | Completed        | 0           | 27d |
| ingress-nginx-admission-patch-6vrl8       | Completed        | 0           | 27d |
| ingress-nginx-controller-5458dd5f6-qdj7w  | Running          | 2 (25d ago) | 27d |

---
```bash
kubectl get svc -n ingress-nginx
---

```bash
| Name                               | Type         | Cluster IP    | External IP  | Ports                       | Age |
| ---------------------------------- | ------------ | ------------- | ------------ | --------------------------- | --- |
| ingress-nginx-controller           | LoadBalancer | 10.109.20.162 | 10.10.37.240 | 80:30705/TCP, 443:30848/TCP | 27d |
| ingress-nginx-controller-admission | ClusterIP    | 10.96.131.139 | <none>       | 443/TCP                     | 27d |
```
---
🧱 Deployment
```bash
kubectl get deploy -n ingress-nginx
```
---

Create Deployment
```bash
kubectl create deploy sample-1 --image=devopsprosamples/next-path-sample-1
kubectl create deploy sample-2 --image=devopsprosamples/next-path-sample-2
kubectl create deploy sample-3 --image=devopsprosamples/next-sample-1
kubectl create deploy sample-4 --image=devopsprosamples/next-sample-2
```
---



