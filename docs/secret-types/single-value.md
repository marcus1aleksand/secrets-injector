# 🔤 Single Value Secrets

Single value secrets map one remote secret (or one property from a JSON secret) to a single key in a Kubernetes Secret.

## Basic Single Value

```yaml
externalsecrets:
  - secret: db-password
    clustersecstore: cluster-azure-backend
    namespace: my-app
    namespacesecretname: db-credentials
    namespacesecretkeyname: password        # Key name in the K8s Secret
    keyvaultsecretname: database-password   # Remote secret name
```

This creates a Kubernetes Secret with one key (`password`) containing the value from the remote secret `database-password`.

## JSON Property Extraction

If your remote secret is a JSON object and you only need one field:

```yaml
externalsecrets:
  - secret: api-key
    clustersecstore: cluster-azure-backend
    namespace: my-app
    namespacesecretname: api-credentials
    namespacesecretkeyname: key
    keyvaultsecretname: api-config
    property: apiKey                        # Extract this JSON property
```

If `api-config` contains `{"apiKey": "abc123", "apiUrl": "https://..."}`, only `abc123` is stored.

## With Custom Labels

```yaml
externalsecrets:
  - secret: db-password
    clustersecstore: cluster-azure-backend
    namespace: my-app
    namespacesecretname: db-credentials
    namespacesecretkeyname: password
    keyvaultsecretname: database-password
    labels:
      app: my-app
```
