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
## ⭐ 5. DEPLOY OTEL DEMO APP WITH HELM
### Install demo + collector
```bash
helm upgrade --install otel-demo open-telemetry/opentelemetry-demo \
  -n otel-demo -f values.yaml
```
<img width="1920" height="1028" alt="image1" src="https://github.com/user-attachments/assets/b50b0ad8-852a-4f46-8124-1897cbc1d95b" />

### Verify pods
```bash
kubectl get pods -n otel-demo
```
Look for:

- frontend
- cartservice
- loadgenerator
- otel-collector

<img width="1920" height="1028" alt="image2" src="https://github.com/user-attachments/assets/60823f69-b506-4e00-8276-f7fd274baad4" />

## ⭐ 6. EXPLORE THE APP
### Port-forward frontend
```bash
kubectl port-forward -n otel-demo svc/frontend-proxy 8080:8080
```
Open browser:
```bash
http://localhost:8080
```
You should see the OTEL demo storefront UI.
<img width="1920" height="1080" alt="image5" src="https://github.com/user-attachments/assets/c95cfd33-cfbe-4a71-8a28-c14e8a950a3f" />

## ⭐ 7. CHECK HONEYCOMB DASHBOARD

1. Open Honeycomb.io
2. Select dataset: **otel-demo**
3. Verify:
    - traces arriving
    - span waterfalls
    - latency graphs
    - service map
    - error rates

Your collector is exporting correctly if you see fresh traces.

<img width="1920" height="1080" alt="image6" src="https://github.com/user-attachments/assets/24bbe843-7653-419c-8f94-af055f8c2550" />
<img width="1920" height="1080" alt="image7" src="https://github.com/user-attachments/assets/b5548b8f-09be-4dd0-bb10-8d7d89f9054f" />

## ⭐ 8. SET UP HONEYCOMB MCP IN VS CODE

### Step 1 — Open VS Code Settings

Search: **Model Context Protocol**
### Step 2 — Add Honeycomb MCP server config

Add in your VS Code’s MCP servers:
```bash
{
  "servers": {
    "honeycomb": {
      "url": "https://mcp.honeycomb.io/mcp",
      "type": "http"
    }
  },
  "inputs": []
}
```
### Step 3 — Restart VS-Code

### Step 4 — OAuth Login

VS Code will pop up a browser:

- Log into Honeycomb
- Click **Allow**

Now VS Code Copilot Chat can query your Honeycomb data directly.

## ⭐ 9. NATURAL-LANGUAGE MCP QUESTIONS

Use these in **Copilot Chat inside VS Code**:

### 🔥 Performance

1. Show me the slowest endpoint in the last 20 minutes.
2. List the top 5 slowest services.
3. Which endpoints have p95 latency > 500ms?
4. What caused the latency spike in the last hour?
5. Show me traces where checkout took more than 500ms.

### ❗ Errors

1. List endpoints with the highest error rate.
2. Which service produced the most errors today?
3. Show me the recent 100 errors grouped by service.

### 📊 Traffic

1. Which service handled the most requests in the last hour?
2. Show me the busiest endpoints right now.
3. What are the top 10 endpoints by request volume?

### 🔍 Debugging

1. Explain the cause of the latest latency spike.
2. Summarize anomalies in the last 30 minutes.
3. Why is checkout slow today?
4. Show me traces where frontend took more than 1 second.
