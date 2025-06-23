# ingress-project

This project sets up the NGINX Ingress Controller on a Kubernetes cluster using a manifest hosted in a public GitHub repository.

## 🔧 Installation

Apply the Ingress controller deployment using `kubectl`:

```bash
kubectl apply -f https://raw.githubusercontent.com/kumardheeraj394/ingress-project/refs/heads/main/ingress-deploy.yaml
---
---
```bash
kubectl get ns
