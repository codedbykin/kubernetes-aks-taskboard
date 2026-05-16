# Kubernetes Deployment on Azure AKS

![Architecture](architecture.png)

---

## Overview

This project demonstrates a production-style Kubernetes deployment on **Azure Kubernetes Service (AKS)**. A 2-tier application — Node.js REST API backend and Nginx frontend — is deployed across multiple Pods with automatic load balancing, health checks, and CPU-based auto-scaling.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Cloud Platform | Microsoft Azure |
| Container Orchestration | Azure Kubernetes Service (AKS) |
| Frontend | Nginx 1.25 (Alpine) |
| Backend | Node.js 20 (Alpine) |
| Networking | Azure Load Balancer, ClusterIP Service |
| Scaling | Horizontal Pod Autoscaler (HPA) |
| Configuration | Kubernetes ConfigMap |
| CLI Tools | Azure CLI, kubectl |
| Infrastructure | 2 x Standard_D2s_v3 nodes, Norway East |

---

## What Kubernetes Does in This Project

| Resource | Purpose |
|---|---|
| **Namespace** | Isolates all resources inside a dedicated taskboard namespace |
| **Deployment** | Maintains 2 replicas at all times — auto-replaces crashed Pods |
| **Service (ClusterIP)** | Internal DNS so the frontend always resolves the backend |
| **Service (LoadBalancer)** | Provisions an Azure public IP automatically via AKS |
| **ConfigMap** | Injects HTML into Nginx without rebuilding the container image |
| **HPA** | Scales backend Pods from 2 to 5 when average CPU exceeds 70% |
| **Liveness Probe** | Restarts a Pod automatically if the app crashes |
| **Readiness Probe** | Withholds traffic until the Pod is fully ready to serve requests |

---

## Project Structure---

## How to Deploy

```bash
# Create resource group
az group create --name rg-aks-taskboard --location norwayeast

# Create AKS cluster
az aks create \
  --resource-group rg-aks-taskboard \
  --name aks-taskboard \
  --node-count 2 \
  --node-vm-size Standard_D2s_v3 \
  --generate-ssh-keys \
  --location norwayeast

# Connect kubectl
az aks get-credentials \
  --resource-group rg-aks-taskboard \
  --name aks-taskboard

# Deploy all manifests
kubectl apply -f k8s/

# Get public IP
kubectl get svc frontend-svc -n taskboard

# Clean up
az group delete --name rg-aks-taskboard --yes --no-wait
```

---

## Key Learnings

- Kubernetes separates concerns cleanly — networking, scaling, and configuration are entirely independent
- The HPA monitors CPU in real time and adds Pods automatically with no manual intervention
- Refreshing the live URL shows different Pod names, demonstrating live load balancing across 2 containers
- ConfigMaps allow configuration updates without rebuilding or redeploying container images
- Liveness and readiness probes are essential for production — they prevent traffic routing to unhealthy Pods
