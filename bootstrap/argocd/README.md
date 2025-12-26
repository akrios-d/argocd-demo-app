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
kind create cluster --name argocd-demo --config kind-config.yaml
kubectl cluster-info
```

------------------------------------------------------------------------

## 2. Install Argo CD

``` bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
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


## Cleanup

``` bash
kind delete cluster --name argocd-demo
```

------------------------------------------------------------------------

## References

-   https://argo-cd.readthedocs.io/
-   https://kind.sigs.k8s.io/
