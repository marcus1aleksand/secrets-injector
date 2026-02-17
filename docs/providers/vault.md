# 🔑 HashiCorp Vault

## Configuration

```yaml
clustersecretstore:
  name: hcp-vault-backend
  providerType: vault
  vault:
    server: "https://vault.example.com"
    path: "secret"
    version: "v2"
    auth:
      tokenName: "vault-token"          # K8s Secret name
      tokenNamespace: "external-secrets" # K8s Secret namespace
      tokenKey: "vault-token"           # Key within the Secret
```

## Prerequisites

### 1. Create a Vault Policy

```hcl
path "secret/data/*" {
  capabilities = ["read"]
}
```

### 2. Create a Token

```bash
vault token create -policy=external-secrets -period=768h
```

### 3. Store the Token in Kubernetes

```bash
kubectl create secret generic vault-token \
  -n external-secrets \
  --from-literal=vault-token=hvs.xxxxxxxxxxxxx
```

!!! warning "Token Renewal"
    Vault tokens expire. Consider using Kubernetes auth method in production for automatic token renewal. The token-based auth shown here requires manual rotation.

## KV Version

| Version | Path Behavior |
|---------|--------------|
| `v1` | Secrets at `<path>/<secret-name>` |
| `v2` | Secrets at `<path>/data/<secret-name>` (ESO handles this automatically) |

## Storing Secrets in Vault

```bash
# Single value
vault kv put secret/my-secret value="my-value"

# Multivalue
vault kv put secret/app-config DB_HOST="db.example.com" DB_PORT="5432"

# JSON
vault kv put secret/argocd-cluster \
  clusterName="prod" \
  host="https://api.prod:6443" \
  caData="..."
```

## HCP Vault (Managed)

For HashiCorp Cloud Platform Vault, use the public endpoint:

```yaml
clustersecretstore:
  name: hcp-vault-backend
  providerType: vault
  vault:
    server: "https://my-vault-cluster.vault.xxxxxxx.aws.hashicorp.cloud:8200"
    path: "secret"
    version: "v2"
    auth:
      tokenName: "hcp-vault-token"
      tokenNamespace: "external-secrets"
      tokenKey: "vault-token"
```
