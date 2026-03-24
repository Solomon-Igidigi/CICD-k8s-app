# CI/CD Pipeline with Docker, GitHub Actions & Kubernetes (Project 4)

## Overview

This project demonstrates how to build a **CI/CD pipeline** that automates the process of building and delivering a containerized application using **GitHub Actions** and **Docker**.

Due to the use of a **local Kubernetes environment (Minikube)**, deployment is handled using a **hybrid approach**:

* CI (GitHub Actions) → builds & pushes Docker image
* CD (Local Kubernetes) → pulls and deploys the updated image manually

---

## Technologies Used

* GitHub Actions (CI/CD)
* Docker (Containerization)
* Docker Hub (Image Registry)
* Kubernetes (Minikube)
* kubectl (Kubernetes CLI)

---

## Architecture

```id="a9a8xf"
Developer → GitHub → GitHub Actions → Docker Hub → Minikube (Local Kubernetes)
```

---

## Project Structure

```id="p4g2n8"
.
├── .github/
│   └── workflows/
│       └── deploy.yaml
├── Dockerfile
├── index.html
├── deployment.yaml
└── service.yaml
```

---

## CI/CD Pipeline Workflow

The pipeline is triggered automatically on every push to the `main` branch.

### Pipeline Steps:

1. Checkout source code
2. Authenticate with Docker Hub using GitHub Secrets
3. Build Docker image
4. Push image to Docker Hub

---

## 🔧 GitHub Actions Workflow

```yaml id="0d3wqj"
name: CI/CD Pipeline

on:
  push:
    branches:
      - main

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:

    - name: Checkout code
      uses: actions/checkout@v3

    - name: Log in to Docker Hub
      uses: docker/login-action@v2
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}

    - name: Build Docker image
      run: docker build -t ${{ secrets.DOCKER_USERNAME }}/k8s-app:v3 .

    - name: Push Docker image
      run: docker push ${{ secrets.DOCKER_USERNAME }}/k8s-app:v3

```

---

## GitHub Secrets

The following secrets are required:

```id="r56b9c"
DOCKER_USERNAME = your-dockerhub-username
DOCKER_PASSWORD = your-dockerhub-access-token
```

---

## Deployment Strategy (Hybrid Approach)

Since Minikube runs locally, GitHub Actions cannot directly deploy to it.

### CI (Automated)

* Builds and pushes Docker images to Docker Hub

### CD (Manual - Local Kubernetes)

After a successful pipeline run, deploy the latest image manually:

```bash id="zldl9z"
kubectl set image deployment/k8s-app-deployment \
k8s-app=yourusername/k8s-app:<image-tag>
```

---

## 🚀 Kubernetes Deployment Configuration

Ensure your `deployment.yaml` uses:

```yaml id="w0cd68"
imagePullPolicy: Always
```

---

## Verification Steps

Check running pods:

```bash id="71t0c3"
kubectl get pods
```

Watch updates live:

```bash id="2cprz0"
kubectl get pods -w
```

---

## Rollback Deployment

Revert to previous version if needed:

```bash id="p7c3qv"
kubectl rollout undo deployment k8s-app-deployment
```

---

## Challenges Faced

* Docker authentication issues due to incorrect token permissions
* Push failures caused by insufficient token scopes
* Limitation of deploying from GitHub Actions to local Minikube

---

## Key Learnings

* CI/CD pipeline design and automation
* Secure credential management using GitHub Secrets
* Kubernetes rolling updates and rollback strategies
* Understanding the separation between CI (cloud) and CD (local cluster)

---

## Outcome

Successfully built a **CI pipeline** that automates Docker image creation and delivery, while integrating with a local Kubernetes cluster using a hybrid deployment approach.

---

## Future Improvements

* Deploy to a cloud Kubernetes cluster (EKS, AKS, GKE)
* Implement full CD automation
* Add health checks (liveness/readiness probes)
* Introduce Blue-Green or Canary deployments

---
