# 📊 Multivalue Secrets

Multivalue secrets extract **all key-value pairs** from a single cloud secret and create them as individual keys in the Kubernetes Secret.

## When to Use

- Your cloud secret stores multiple key-value pairs (e.g., a JSON object in Azure Key Vault)
- You want all keys extracted automatically without listing them individually

## Configuration

```yaml
externalsecrets:
  - secret: app-config          # ClusterExternalSecret name
    multivalue: true             # Enable multi-key extraction
    clustersecstore: cluster-azure-backend
    namespace: my-app
    namespacesecretname: app-config
    keyvaultsecretname: my-app-config
```

## How It Works

The template generates a `dataFrom.extract` block:

```yaml
dataFrom:
  - extract:
      key: my-app-config
```

This tells ESO to fetch the entire secret and extract each top-level key as a separate entry in the Kubernetes Secret.

## Example

If your Azure Key Vault secret `my-app-config` contains:

```json
{
  "DB_HOST": "db.example.com",
  "DB_PORT": "5432",
  "DB_PASSWORD": "s3cret"
}
```

The resulting Kubernetes Secret will have three keys: `DB_HOST`, `DB_PORT`, and `DB_PASSWORD`.

## With Custom Labels

```yaml
externalsecrets:
  - secret: app-config
    multivalue: true
    clustersecstore: cluster-azure-backend
    namespace: my-app
    namespacesecretname: app-config
    keyvaultsecretname: my-app-config
    labels:
      app.kubernetes.io/part-of: my-app
```
