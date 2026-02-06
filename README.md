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
## ⭐ 2. HONEYCOMB.IO ACCOUNT
1. Go to: [**https://ui.honeycomb.io**](https://ui.honeycomb.io/)
2. Sign up (Google or email)
3. Create an **Environment**
4. Go to **Settings → API Keys**
5. Copy your **Team API Key**

Dataset name we will use: **otel-demo**
