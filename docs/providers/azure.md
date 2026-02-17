# 🔑 Azure Key Vault

## Authentication Methods

### Managed Identity (Recommended)

Use Azure Managed Identity for passwordless authentication from AKS:

```yaml
clustersecretstore:
  name: cluster-azure-backend
  providerType: azurekv
  azurekv:
    tenantid: "00000000-0000-0000-0000-000000000000"
    vaulturl: "https://my-keyvault.vault.azure.net"
    identityid: "00000000-0000-0000-0000-000000000000"  # Managed Identity Client ID
```

#### Prerequisites

1. **User-Assigned Managed Identity** with `Key Vault Secrets User` role on the Key Vault
2. **Federated credential** or **pod identity** binding the managed identity to the ESO service account

```bash
# Assign the role
az role assignment create \
  --role "Key Vault Secrets User" \
  --assignee <managed-identity-client-id> \
  --scope /subscriptions/<sub>/resourceGroups/<rg>/providers/Microsoft.KeyVault/vaults/<vault>
```

### Service Principal

For environments without managed identity support:

```yaml
clustersecretstore:
  name: cluster-azure-backend
  providerType: azurekv
  azurekv:
    tenantid: "00000000-0000-0000-0000-000000000000"
    vaulturl: "https://my-keyvault.vault.azure.net"
    clientid:
      name: azure-secret-sp       # K8s Secret name
      namespace: eso               # K8s Secret namespace
      id: ClientID                 # Key within the Secret
    clientsecret:
      name: azure-secret-sp
      namespace: eso
      id: ClientSecret
```

!!! warning "Pre-create the Secret"
    You must manually create the `azure-secret-sp` Kubernetes Secret containing the Service Principal credentials before installing the chart.

## Storing Secrets in Azure Key Vault

### Single Value

```bash
az keyvault secret set --vault-name my-vault \
  --name my-secret --value "my-value"
```

### Multivalue (JSON)

```bash
az keyvault secret set --vault-name my-vault \
  --name app-config \
  --value '{"DB_HOST":"db.example.com","DB_PORT":"5432","DB_PASS":"secret"}'
```

### TLS Certificate

```bash
az keyvault secret set --vault-name my-vault \
  --name wildcard-cert-crt --value "$(cat cert.pem)"
az keyvault secret set --vault-name my-vault \
  --name wildcard-cert-key --value "$(cat key.pem)"
```

### ArgoCD Cluster

```bash
az keyvault secret set --vault-name my-vault \
  --name argocd-prod-cluster \
  --value '{"clusterName":"prod","host":"https://api.prod:6443","caData":"...","certData":"...","keyData":"..."}'
```
