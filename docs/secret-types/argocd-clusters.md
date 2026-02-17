# 🏗️ ArgoCD Cluster Secrets

Register Kubernetes clusters in ArgoCD by creating secrets with the `argocd.argoproj.io/secret-type: cluster` label.

## Certificate Authentication

```yaml
externalsecrets:
  - secret: prod-cluster
    argocd: true
    clustersecstore: cluster-azure-backend
    namespace: argocd
    namespacesecretname: prod-cluster-secret
    keyvaultsecretname: argocd-prod-cluster
```

### Required Properties in Cloud Secret

Your cloud secret must be a JSON object with these keys:

```json
{
  "clusterName": "prod-cluster",
  "host": "https://prod-api.example.com:6443",
  "caData": "<base64-encoded-ca-cert>",
  "certData": "<base64-encoded-client-cert>",
  "keyData": "<base64-encoded-client-key>"
}
```

## Bearer Token Authentication

```yaml
externalsecrets:
  - secret: staging-cluster
    argocd: true
    argocdBearerToken: true
    clustersecstore: cluster-azure-backend
    namespace: argocd
    namespacesecretname: staging-cluster-secret
    keyvaultsecretname: argocd-staging-cluster
```

### Required Properties in Cloud Secret

```json
{
  "clusterName": "staging-cluster",
  "host": "https://staging-api.example.com:6443",
  "caData": "<base64-encoded-ca-cert>",
  "bearerToken": "<service-account-token>"
}
```

## Generated Secret Structure

The template creates an Opaque secret with:

- **Labels:** `argocd.argoproj.io/secret-type: cluster`
- **Keys:** `name`, `server`, `config`

The `config` key contains the TLS configuration JSON that ArgoCD expects.

!!! tip "Custom Labels"
    You can add extra labels (e.g., for ArgoCD ApplicationSets) using the `labels` field.
