# 🏷️ Custom Labels & Types

## Custom Secret Types

Create secrets with non-default types (e.g., `kubernetes.io/dockerconfigjson`):

```yaml
externalsecrets:
  - secret: docker-registry
    type: "kubernetes.io/dockerconfigjson"
    clustersecstore: cluster-azure-backend
    namespace: my-app
    namespacesecretname: registry-creds
    namespacesecretkeyname: .dockerconfigjson
    keyvaultsecretname: docker-registry-config
```

The template wraps the value in a template block with the specified type, creating a properly typed Kubernetes Secret.

## Custom Labels

Any secret type supports the `labels` field:

```yaml
externalsecrets:
  - secret: labeled-secret
    multivalue: true
    clustersecstore: cluster-azure-backend
    namespace: my-app
    namespacesecretname: my-secret
    keyvaultsecretname: my-secret
    labels:
      app.kubernetes.io/part-of: platform
      environment: production
      team: sre
```

Labels are applied to the generated Kubernetes Secret's metadata, making them available for selectors, policies, and tooling.

## Namespace Selectors

By default, secrets target a single namespace via `namespace`. For advanced use cases, use `namespaceSelector`:

```yaml
externalsecrets:
  - secret: shared-config
    multivalue: true
    clustersecstore: cluster-azure-backend
    namespacesecretname: shared-config
    keyvaultsecretname: shared-config
    namespaceSelector:
      matchLabels:
        environment: production
```

This deploys the secret to **all namespaces** matching the label selector.
