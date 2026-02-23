# gitops-cloudnativePG

TLS-сертификаты хранятся в секрете vault-tls-server, который должен быть создан заранее (например, через cert-manager).

Для любого сервиса (например, Vault UI) в аннотациях Ingress укажите:

```yaml
cert-manager.io/cluster-issuer: internal-ca-issuer
```

Корневой сертификат CA хранится в секрете root-ca-secret (в поле ca.crt). Его нужно извлечь и добавить в доверенные на всех клиентах (браузеры, curl и т.д.):

```
kubectl get secret root-ca-secret -n cert-manager -o jsonpath='{.data.ca\.crt}' | base64 -d > ca.crt
```

kubectl port-forward -n vault svc/vault 8200:8200
http://localhost:8200

sudo nano /etc/hosts
127.0.0.1 vault.local

k cnpg status demo-cluster
k cnpg maintenance set --all-namespaces
