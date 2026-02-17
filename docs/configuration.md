# 📋 Configuration Reference

Complete reference for all `values.yaml` parameters.

## ClusterSecretStore

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `clustersecretstore.name` | string | `cluster-azure-backend` | Name of the ClusterSecretStore resource |
| `clustersecretstore.providerType` | string | `azurekv` | Provider: `azurekv`, `aws`, or `vault` |

### Azure Key Vault Provider

| Parameter | Type | Description |
|-----------|------|-------------|
| `clustersecretstore.azurekv.tenantid` | string | Azure AD Tenant ID |
| `clustersecretstore.azurekv.vaulturl` | string | Key Vault URL (e.g., `https://my-vault.vault.azure.net`) |
| `clustersecretstore.azurekv.identityid` | string | Managed Identity Client ID (for MI auth) |
| `clustersecretstore.azurekv.clientid.name` | string | K8s Secret name for SP Client ID |
| `clustersecretstore.azurekv.clientid.namespace` | string | K8s Secret namespace |
| `clustersecretstore.azurekv.clientid.id` | string | Key within the K8s Secret |
| `clustersecretstore.azurekv.clientsecret.name` | string | K8s Secret name for SP Client Secret |
| `clustersecretstore.azurekv.clientsecret.namespace` | string | K8s Secret namespace |
| `clustersecretstore.azurekv.clientsecret.id` | string | Key within the K8s Secret |

### AWS Secrets Manager Provider

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `clustersecretstore.aws.region` | string | `us-east-1` | AWS region |
| `clustersecretstore.aws.auth.serviceAccountName` | string | — | K8s Service Account with IRSA |
| `clustersecretstore.aws.auth.serviceAccountNamespace` | string | — | SA namespace |

### HashiCorp Vault Provider

| Parameter | Type | Description |
|-----------|------|-------------|
| `clustersecretstore.vault.server` | string | Vault server URL |
| `clustersecretstore.vault.path` | string | Secrets engine mount path |
| `clustersecretstore.vault.version` | string | KV engine version (`v1` or `v2`) |
| `clustersecretstore.vault.auth.tokenName` | string | K8s Secret name containing Vault token |
| `clustersecretstore.vault.auth.tokenNamespace` | string | K8s Secret namespace |
| `clustersecretstore.vault.auth.tokenKey` | string | Key within the K8s Secret |

## External Secrets

Each entry in the `externalsecrets` list supports:

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `secret` | string | **required** | ClusterExternalSecret resource name |
| `clustersecstore` | string | **required** | Target ClusterSecretStore name |
| `namespace` | string | — | Target namespace (used for namespace matching) |
| `namespacesecretname` | string | **required** | Name of the K8s Secret to create |
| `keyvaultsecretname` | string | **required** | Remote secret key/name |
| `namespacesecretkeyname` | string | — | Key name in the K8s Secret (single value) |
| `multivalue` | bool | `false` | Extract all keys from remote secret |
| `argocd` | bool | `false` | Create as ArgoCD cluster secret |
| `argocdBearerToken` | bool | `false` | Use bearer token auth (requires `argocd: true`) |
| `argocdRepoCreds` | bool | `false` | Create as ArgoCD repo credentials |
| `contactpoint` | bool | `false` | Create as Grafana contact point |
| `type` | string | — | K8s Secret type (e.g., `kubernetes.io/tls`) |
| `namespacesecretkeynamecrt` | string | — | TLS cert key name (for TLS type) |
| `namespacesecretkeynamekey` | string | — | TLS private key name (for TLS type) |
| `property` | string | — | Extract specific JSON property |
| `labels` | map | — | Custom labels for the K8s Secret |
| `namespaceSelector` | object | — | Custom namespace selector (overrides `namespace`) |

## Sync Configuration

These are configured in the Helm templates (not in values):

| Setting | Value | Location |
|---------|-------|----------|
| Refresh time (CES) | `1m` | `clusterexternalsecrets.yaml` |
| Refresh interval (ES) | `5m` | `clusterexternalsecrets.yaml` |
| Creation policy | `Owner` | `clusterexternalsecrets.yaml` |
