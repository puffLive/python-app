# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Simple Flask application designed for Kubernetes deployment on kind (Kubernetes in Docker). The app exposes API endpoints for health checks and system details.

## Application Architecture

**Application Structure:**
- Single Flask application in [src/app.py](src/app.py)
- Two REST endpoints:
  - `/app/v1/details` - Returns current time and hostname
  - `/app/v1/healthz` - Health check endpoint
- Runs on port 5000 inside container
- Uses Python 3.13-slim base image

**Kubernetes Configuration:**
- Service exposes port 8080 externally, forwards to container port 5000
- Ingress routes HTTP traffic on port 80 to the service
- kind cluster configured with port mappings for ports 80 and 443

## Commands

### Local Development
```bash
# Install dependencies
pip install -r requirements.txt

# Run application locally
python src/app.py
# App will be available at http://localhost:5000
```

### Docker
```bash
# Build Docker image
docker build -t kevinhood/python-app:v2 .

# Run container locally
docker run -p 5000:5000 kevinhood/python-app:v2
```

### Kubernetes (kind)
```bash
# Create kind cluster with config
kind create cluster --config kind-config.yaml

# Deploy application
kubectl apply -f k8s/deploy.yaml
kubectl apply -f k8s/service.yaml
kubectl apply -f k8s/ingress.yaml

# Check deployment
kubectl get pods
kubectl get svc
kubectl get ingress

# View logs
kubectl logs -l app=python-app

# Delete cluster
kind delete cluster
```

### Testing Endpoints
```bash
# Local testing
curl http://localhost:5000/app/v1/healthz
curl http://localhost:5000/app/v1/details

# Testing on kind cluster (after deploying ingress controller)
curl http://localhost/app/v1/healthz
curl http://localhost/app/v1/details
```

## Dependencies

- flask==3.0.3
