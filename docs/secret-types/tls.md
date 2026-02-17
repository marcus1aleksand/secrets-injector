# 🔒 TLS Certificates

Create `kubernetes.io/tls` type secrets by fetching certificate and private key from separate remote secrets.

## Configuration

```yaml
externalsecrets:
  - secret: wildcard-tls
    type: "kubernetes.io/tls"
    clustersecstore: cluster-azure-backend
    namespace: ingress-nginx
    namespacesecretname: wildcard-cert
    namespacesecretkeynamecrt: tls.crt      # Key for the certificate
    namespacesecretkeynamekey: tls.key      # Key for the private key
    keyvaultsecretname: wildcard-cert       # Base name in cloud provider
```

## How It Works

The template fetches **two** remote secrets using the base name with suffixes:

- `wildcard-cert-crt` → stored as `tls.crt`
- `wildcard-cert-key` → stored as `tls.key`

!!! important "Naming Convention"
    Store your certificate and key as separate secrets in your cloud provider with `-crt` and `-key` suffixes on the base name.

## Cloud Provider Setup

### Azure Key Vault

Create two secrets in your Key Vault:

```bash
az keyvault secret set --vault-name my-vault \
  --name wildcard-cert-crt \
  --value "$(cat certificate.pem)"

az keyvault secret set --vault-name my-vault \
  --name wildcard-cert-key \
  --value "$(cat private-key.pem)"
```

### AWS Secrets Manager

```bash
aws secretsmanager create-secret \
  --name wildcard-cert-crt \
  --secret-string "$(cat certificate.pem)"

aws secretsmanager create-secret \
  --name wildcard-cert-key \
  --secret-string "$(cat private-key.pem)"
```

## Use with Ingress

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-app
spec:
  tls:
    - secretName: wildcard-cert  # References the secret created by Secrets Injector
      hosts:
        - "*.example.com"
```
