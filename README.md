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

## ⭐ 3. INSTALL OTEL HELM CHART
### Add OTEL Helm repo
```bash
helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts
helm repo update
```
### Create namespace
```bash
kubectl create namespace otel-demo
```
## ⭐ 4. CREATE values.yaml
Create a file named **values.yaml** with:
```bash
opentelemetry-collector:
  enabled: true

  config:
    receivers:
      otlp:
        protocols:
          http: {}
          grpc: {}

    processors:
      batch: {}

    exporters:
      otlphttp/honeycomb:
        endpoint: https://api.honeycomb.io
        headers:
          x-honeycomb-team: "<YOUR_API_KEY>"
          x-honeycomb-dataset: "otel-demo"

    service:
      pipelines:
        traces:
          receivers: [otlp]
          processors: [batch]
          exporters: [otlphttp/honeycomb]

        metrics:
          receivers: [otlp]
          processors: [batch]
          exporters: [otlphttp/honeycomb]

        logs:
          receivers: [otlp]
          processors: [batch]
          exporters: [otlphttp/honeycomb]
```
Replace <YOUR_API_KEY> with your Honeycomb key.















