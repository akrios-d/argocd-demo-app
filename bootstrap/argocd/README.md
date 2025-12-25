# Argo CD Local Demo (Kind)

This repository demonstrates a **local Argo CD GitOps workflow** using
**Kind** and a simple **NGINX Kubernetes application**.

------------------------------------------------------------------------

## Prerequisites

Make sure you have the following installed:

-   Docker
-   kubectl
-   kind
-   git

Optional: - Argo CD CLI (`argocd`)

------------------------------------------------------------------------

## Architecture Overview

-   Local Kubernetes cluster (Kind)
-   Argo CD installed in `argocd` namespace
-   Git repository as the source of truth
-   Kubernetes manifests deployed via GitOps

------------------------------------------------------------------------

## 1. Create a Local Kubernetes Cluster

``` bash
kind create cluster --name argocd-demo
kubectl cluster-info
```

------------------------------------------------------------------------

## 2. Install Argo CD

``` bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
kubectl get pods -n argocd
```

------------------------------------------------------------------------

## 3. Access the Argo CD UI

``` bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Open: https://localhost:8080

Get admin password:

``` bash
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d
```

------------------------------------------------------------------------

## 4. Repository Structure

    .
    ├── application.yaml
    └── k8s
        ├── deployment.yaml
        └── service.yaml

------------------------------------------------------------------------

## 5. Kubernetes Manifests

### Deployment

``` yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:1.25
          ports:
            - containerPort: 80
```

### Service

``` yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx
spec:
  type: ClusterIP
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
```

------------------------------------------------------------------------

## 6. Argo CD Application

``` yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: nginx-demo
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/YOUR_USERNAME/argocd-demo-app.git
    targetRevision: HEAD
    path: k8s
  destination:
    server: https://kubernetes.default.svc
    namespace: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

------------------------------------------------------------------------

## 7. Access the Application

``` bash
kubectl port-forward svc/nginx 8081:80
```

Open: http://localhost:8081

------------------------------------------------------------------------

## Cleanup

``` bash
kind delete cluster --name argocd-demo
```

------------------------------------------------------------------------

## References

-   https://argo-cd.readthedocs.io/
-   https://kind.sigs.k8s.io/
