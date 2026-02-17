# 🚀 Getting Started

Get your first external secret syncing in under 5 minutes.

## Prerequisites

- **Kubernetes** cluster (v1.28+)
- **Helm** v3.x installed
- **External Secrets Operator** [installed](https://external-secrets.io/latest/introduction/getting-started/)
- Access to a supported secret provider

!!! warning "External Secrets Operator Required"
    The Secrets Injector creates ESO custom resources (`ClusterSecretStore`, `ClusterExternalSecret`). The ESO must be running in your cluster for these resources to be reconciled.

## Step 1: Install External Secrets Operator

If you haven't already:

```bash
helm repo add external-secrets https://charts.external-secrets.io
helm install external-secrets external-secrets/external-secrets \
  -n external-secrets --create-namespace
```

## Step 2: Create Your Values File

=== "Azure Key Vault"

    ```yaml
    clustersecretstore:
      name: cluster-azure-backend
      providerType: azurekv
      azurekv:
        tenantid: "your-tenant-id"
        vaulturl: "https://your-vault.vault.azure.net"
        identityid: "your-managed-identity-client-id"

    externalsecrets:
      - secret: my-first-secret
        multivalue: true
        clustersecstore: cluster-azure-backend
        namespace: default
        namespacesecretname: my-app-config
        keyvaultsecretname: my-app-config
    ```

=== "AWS Secrets Manager"

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
      - secret: my-first-secret
        multivalue: true
        clustersecstore: cluster-aws-backend
        namespace: default
        namespacesecretname: my-app-config
        keyvaultsecretname: my-app-config
    ```

=== "HashiCorp Vault"

    ```yaml
    clustersecretstore:
      name: hcp-vault-backend
      providerType: vault
      vault:
        server: "https://vault.example.com"
        path: "secret"
        version: "v2"
        auth:
          tokenName: "vault-token"
          tokenNamespace: "external-secrets"
          tokenKey: "vault-token"

    externalsecrets:
      - secret: my-first-secret
        multivalue: true
        clustersecstore: hcp-vault-backend
        namespace: default
        namespacesecretname: my-app-config
        keyvaultsecretname: my-app-config
    ```

## Step 3: Install Secrets Injector

```bash
helm install secrets-injector \
  oci://ghcr.io/marcus1aleksand/helm-charts/secrets-injector \
  -f values.yaml
```

## Step 4: Verify

```bash
# Check the ClusterSecretStore is ready
kubectl get clustersecretstore

# Check the ClusterExternalSecret was created
kubectl get clusterexternalsecret

# Check the ExternalSecret in the target namespace
kubectl get externalsecret -n default

# Verify the Kubernetes Secret was created
kubectl get secret my-app-config -n default
```

!!! success "Done!"
    Your secret is now synced from your cloud provider to Kubernetes and will auto-refresh every 5 minutes.

## Next Steps

- Learn about all [Secret Types](secret-types/index.md)
- Configure additional [Cloud Providers](providers/azure.md)
- See the full [Configuration Reference](configuration.md)
