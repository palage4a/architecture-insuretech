# Task 2 — Dynamic Scaling (HPA)

## What was done

Configured Horizontal Pod Autoscaler (HPA) for memory-based scaling of a test application in Minikube.

## How to reproduce

```bash
# 1. Start Minikube and enable metrics-server
minikube start
minikube addons enable metrics-server

# 2. Apply manifests
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl apply -f hpa.yaml

# 3. Wait for metrics to become available (~2 min)
kubectl get hpa -w

# 4. Get service URL and generate load
SERVICE_URL=$(minikube service scaletestapp --url)
locust -f locustfile.py --headless --users 200 --spawn-rate 50 --run-time 1m --host "$SERVICE_URL"

# 5. Verify scaling
kubectl get hpa
kubectl get pods
kubectl describe hpa scaletestapp-hpa
```

Expected result: HPA scales replicas from 1 to 2+ when memory utilization exceeds 80%.

## Evidence

| File | Description |
|------|-------------|
| `scaletestapp.info` | Deployment description showing replicas state and conditions |
| `scaletestapp.events.png` | Dashboard screenshot showing pod count increase after load |
