# Deployment Guide 

## Project Overview

This repository contains Kubernetes manifests and Helm charts for deploying the Silver Enigma application. The application consists of three main components:

- **Frontend**: A React-based application served via an Nginx container.
- **Backend**: A FastAPI-based service that interacts with a PostgreSQL database.
- **Database**: A PostgreSQL instance used for data persistence.

## Directory Structure

```
.
├── helm-chart/
│   ├── templates/
│   │   ├── backend-deployment.yaml
│   │   ├── backend-service.yaml
│   │   ├── frontend-deployment.yaml
│   │   ├── frontend-service.yaml
│   │   ├── postgres-deployment.yaml
│   │   ├── postgres-service.yaml
│   │   ├── postgres-pvc.yaml
│   │   ├── ingress.yaml
│   ├── values.yaml
│   ├── Chart.yaml
│   ├── templates/
├── kustomize/
│   ├── base/
│   │   ├── kustomization.yaml
│   │   ├── backend-deployment.yaml
│   │   ├── backend-service.yaml
│   │   ├── frontend-deployment.yaml
│   │   ├── frontend-service.yaml
│   │   ├── postgres-deployment.yaml
│   │   ├── postgres-service.yaml
│   │   ├── postgres-pvc.yaml
│   │   ├── ingress.yaml
│   ├── overlays/
│   │   ├── dev/
│   │   │   ├── kustomization.yaml
│   │   │   ├── values-dev.yaml
│   │   ├── prod/
│   │   │   ├── kustomization.yaml
│   │   │   ├── values-prod.yaml
```

## Deployment with Helm

### Prerequisites

- Kubernetes cluster (Minikube, Kind, or cloud-based cluster)
- Helm installed
- Ingress controller enabled (for local development, `minikube addons enable ingress`)

### Deploying the Application

```sh
helm upgrade --install myapp ./helm-chart
```

This command deploys the application using the Helm chart in `helm-chart/`.

### Uninstalling the Application

```sh
helm uninstall myapp
```

## Deployment with Kustomize

### Prerequisites

- Kubernetes cluster
- `kubectl` installed
- `kustomize` plugin available (or use `kubectl kustomize`)

### Deploying to Dev

```sh
kubectl apply -k kustomize/overlays/dev
```

### Deploying to Prod

```sh
kubectl apply -k kustomize/overlays/prod
```

### Uninstalling

```sh
kubectl delete -k kustomize/overlays/dev
```

## Known Issues

### ❗ Frontend is running in development mode

- **Issue**: The frontend container runs in development mode and does not serve built static files.
- **Current State**: The React app is served using `serve -s build`, but the build step is missing.
- **Workaround**: Add the `build` step in Dockerfile or entrypoint script inside the container
- **Workaround**: Use an init-container to build the frontend before running the main container, or mount a prebuilt build/ directory using a ConfigMap/Volume.

## Notes

- Ensure the correct `REACT_APP_BACKEND_API_BASE_URL` is set in the frontend environment variables to correctly point to the backend.
- PostgreSQL service references should be updated if moving from Helm to Kustomize (`postgres-service.yaml` in `kustomize/base/`).

