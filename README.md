# Honeycomb MCP Demo

### ⭐ INTRODUCTION
This guide walks you through a complete observability workflow:

- running Kubernetes locally with **Minikube**
- deploying the **OpenTelemetry Demo App**
- exporting data to **Honeycomb.io**
- analyzing telemetry using **Honeycomb MCP in VS Code**
- asking natural-language observability questions

By the end, you’ll have a full AI-powered observability stack running locally.

## ⭐ 1. MINIKUBE SETUP

### Start Minikube
```bash
minikube start --cpus=4 --memory=6g
```
### Verify node
```bash
kubectl get nodes
```
#### Check cluster connectivity
```bash
kubectl get pods -A
```
