# Kubernetes Deployment on Azure AKS

![Architecture](https://raw.githubusercontent.com/codedbykin/kubernetes-aks-taskboard/main/Project%205.png)

---

## Overview

This project demonstrates a production-style Kubernetes deployment on **Azure Kubernetes Service (AKS)**. A 2-tier application — Node.js REST API backend and Nginx frontend — is deployed across multiple Pods with automatic load balancing, health checks, and CPU-based auto-scaling.

**Deployed and verified live at:** `http://20.100.29.38`

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
| Infrastructure | 2 × Standard_D2s_v3 nodes, Norway East |

---

## What Kubernetes Does in This Project

| Resource | Purpose |
|---|---|
| **Namespace** | Isolates all resources inside a dedicated `taskboard` namespace |
| **Deployment** | Maintains 2 replicas at all times — auto-replaces crashed Pods |
| **Service (ClusterIP)** | Internal DNS entry so the frontend always resolves the backend |
| **Service (LoadBalancer)** | Provisions an Azure public IP automatically via AKS |
| **ConfigMap** | Injects HTML into the Nginx container without rebuilding the image |
| **HPA** | Scales backend Pods from 2 → 5 when average CPU exceeds 70% |
| **Liveness Probe** | Restarts a Pod automatically if the app crashes |
| **Readiness Probe** | Withholds traffic until the Pod is fully ready to serve requests |

---

## Project Structure
kubernetes-aks-taskboard/
└── k8s/
    ├── 00-namespace.yaml      # Namespace: taskboard
    ├── 01-backend.yaml        # Node.js Deployment + ClusterIP Service
    ├── 02-frontend.yaml       # Nginx Deployment + LoadBalancer Service + ConfigMap
    └── 03-hpa.yaml            # Horizontal Pod Autoscaler
