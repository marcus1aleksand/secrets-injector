# 💡 Examples

Real-world configuration examples for common scenarios.

## Full Production Setup (Azure)

A complete `values.yaml` managing secrets for a production cluster:

```yaml
clustersecretstore:
  name: cluster-azure-backend
  providerType: azurekv
  azurekv:
    tenantid: "12345678-1234-1234-1234-123456789abc"
    vaulturl: "https://prod-keyvault.vault.azure.net"
    identityid: "abcdef01-2345-6789-abcd-ef0123456789"

externalsecrets:
  # Application database credentials (multivalue)
  - secret: app-db-creds
    multivalue: true
    clustersecstore: cluster-azure-backend
    namespace: production
    namespacesecretname: db-credentials
    keyvaultsecretname: prod-db-credentials

  # Wildcard TLS certificate
  - secret: wildcard-tls
    type: "kubernetes.io/tls"
    clustersecstore: cluster-azure-backend
    namespace: ingress-nginx
    namespacesecretname: wildcard-cert
    namespacesecretkeynamecrt: tls.crt
    namespacesecretkeynamekey: tls.key
    keyvaultsecretname: wildcard-example-com

  # ArgoCD cluster registration
  - secret: staging-cluster
    argocd: true
    argocdBearerToken: true
    clustersecstore: cluster-azure-backend
    namespace: argocd
    namespacesecretname: staging-cluster
    keyvaultsecretname: argocd-staging-cluster

  # ArgoCD GitHub credentials
  - secret: github-creds
    argocdRepoCreds: true
    clustersecstore: cluster-azure-backend
    namespace: argocd
    namespacesecretname: github-repo-creds
    keyvaultsecretname: argocd-github-creds

  # Grafana alerting
  - secret: grafana-alerts
    contactpoint: true
    clustersecstore: cluster-azure-backend
    namespace: monitoring
    namespacesecretname: grafana-contact-points
    keyvaultsecretname: grafana-contactpoints

  # Docker registry credentials
  - secret: docker-reg
    type: "kubernetes.io/dockerconfigjson"
    clustersecstore: cluster-azure-backend
    namespace: production
    namespacesecretname: registry-creds
    namespacesecretkeyname: .dockerconfigjson
    keyvaultsecretname: docker-registry-config

  # Simple API key
  - secret: api-key
    clustersecstore: cluster-azure-backend
    namespace: production
    namespacesecretname: external-api
    namespacesecretkeyname: api-key
    keyvaultsecretname: external-api-key
```

## Multi-Cloud Setup

### AWS Values (`values-aws.yaml`)

```yaml
clustersecretstore:
  name: cluster-aws-backend
  providerType: aws
  aws:
    region: "us-east-1"
    auth:
      serviceAccountName: "external-secrets-sa"
      serviceAccountNamespace: "external-secrets"

externalsecrets:
  - secret: aws-app-config
    multivalue: true
    clustersecstore: cluster-aws-backend
    namespace: my-app
    namespacesecretname: app-config
    keyvaultsecretname: prod/my-app/config
```

### Vault Values (`values-vault.yaml`)

```yaml
clustersecretstore:
  name: hcp-vault-backend
  providerType: vault
  vault:
    server: "https://vault.internal.example.com"
    path: "secret"
    version: "v2"
    auth:
      tokenName: "vault-token"
      tokenNamespace: "external-secrets"
      tokenKey: "vault-token"

externalsecrets:
  - secret: vault-app-config
    multivalue: true
    clustersecstore: hcp-vault-backend
    namespace: my-app
    namespacesecretname: vault-config
    keyvaultsecretname: app-config
```

### Install Both

```bash
helm install secrets-azure \
  oci://ghcr.io/marcus1aleksand/helm-charts/secrets-injector \
  -f values-azure.yaml

helm install secrets-aws \
  oci://ghcr.io/marcus1aleksand/helm-charts/secrets-injector \
  -f values-aws.yaml

helm install secrets-vault \
  oci://ghcr.io/marcus1aleksand/helm-charts/secrets-injector \
  -f values-vault.yaml
```

## Namespace Selector Example

Deploy the same secret to all namespaces with a specific label:

```yaml
externalsecrets:
  - secret: shared-tls
    type: "kubernetes.io/tls"
    clustersecstore: cluster-azure-backend
    namespacesecretname: shared-tls-cert
    namespacesecretkeynamecrt: tls.crt
    namespacesecretkeynamekey: tls.key
    keyvaultsecretname: shared-tls
    namespaceSelector:
      matchLabels:
        inject-tls: "true"
```

Then label namespaces to opt in:

```bash
kubectl label namespace my-app inject-tls=true
kubectl label namespace other-app inject-tls=true
```
