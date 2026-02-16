# gitops-cloudnativePG

TLS-сертификаты хранятся в секрете vault-tls-server, который должен быть создан заранее (например, через cert-manager).

Для любого сервиса (например, Vault UI) в аннотациях Ingress укажите:

```yaml
cert-manager.io/cluster-issuer: my-ca-issuer
```
Корневой сертификат CA хранится в секрете my-ca-secret (в поле ca.crt). Его нужно извлечь и добавить в доверенные на всех клиентах (браузеры, curl и т.д.):
```
kubectl get secret my-ca-secret -n cert-manager -o jsonpath='{.data.ca\.crt}' | base64 -d > ca.crt
```
kubectl port-forward -n vault svc/vault 8200:8200
http://localhost:8200

sudo nano /etc/hosts
127.0.0.1 vault.local
