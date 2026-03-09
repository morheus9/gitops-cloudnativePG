# gitops-cloudnativePG

## Quick Start: Install to Fresh Cluster

### Prerequisites

- Kubernetes cluster is running
- `kubectl` configured to access the cluster

### Installation Steps

```bash
# 1. Install ArgoCD
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

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

### What Gets Deployed

| Chart            | Sync Wave | Description         |
| ---------------- | --------- | ------------------- |
| cert-manager     | 0         | TLS certificates    |
| cloudnative-pg   | 1         | PostgreSQL operator |
| external-secrets | 2         | Secrets management  |
| vault            | 3         | Secret storage      |

All charts auto-sync with `prune: true` and `selfHeal: true`.

---

## TLS Certificates

TLS-сертификаты хранятся в секрете vault-tls-server, который должен быть создан заранее (например, через cert-manager).

Для любого сервиса (например, Vault UI) в аннотациях Ingress укажите:

```yaml
cert-manager.io/cluster-issuer: internal-ca-issuer
```

Корневой сертификат CA хранится в секрете root-ca-secret (в поле ca.crt). Его нужно извлечь и добавить в доверенные на всех клиентах (браузеры, curl и т.д.):

```bash
kubectl get secret root-ca-secret -n cert-manager -o jsonpath='{.data.ca\.crt}' | base64 -d > ca.crt
```

## Vault Access

```bash
kubectl port-forward -n vault svc/vault 8200:8200
```

Open: http://localhost:8200

```bash
sudo nano /etc/hosts
127.0.0.1 vault.local
```

## CloudNativePG Commands

```bash
kubectl cnpg status demo-cluster
kubectl cnpg maintenance set --all-namespaces
```
