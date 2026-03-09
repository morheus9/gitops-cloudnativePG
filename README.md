# gitops-cloudnativePG

## Quick Start: Install to Fresh Cluster

### Prerequisites

- Kubernetes cluster is running
- `kubectl` configured to access the cluster

> **Note:** The `--server-side --force-conflicts` flags are required for ArgoCD v3.x due to large CRD annotations that exceed the client-side apply limit (262144 bytes). Server-side apply handles this correctly.

### Installation Steps

```bash
# 1. Install ArgoCD
kubectl create namespace argocd
kubectl apply --server-side --force-conflicts -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# 2. Wait for ArgoCD to be ready
kubectl wait --for=condition=available --timeout=600s deployment/argocd-server -n argocd

# 3. Get admin password
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d && echo

# 4. Apply AppProjects
kubectl apply -f argocd/projects/

# 5. Apply Root Applications (this deploys all charts)
kubectl apply -f root-apps/

# 6. Verify
kubectl get applications -n argocd
```

### Access ArgoCD UI

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
Open: https://localhost:8080 (admin / password from step 3)
