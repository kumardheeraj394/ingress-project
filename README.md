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
