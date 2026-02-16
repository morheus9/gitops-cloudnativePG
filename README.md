# gitops-cloudnativePG

TLS-сертификаты хранятся в секрете vault-tls-server, который должен быть создан заранее (например, через cert-manager).

kubectl port-forward -n vault svc/vault 8200:8200
http://localhost:8200

sudo nano /etc/hosts
127.0.0.1 vault.local
