# 🔑 Secret Types

The Secrets Injector supports a wide variety of secret types through a single, unified configuration interface. Each entry in the `externalsecrets` list in your `values.yaml` can produce a different type of Kubernetes Secret.

## Supported Types

| Type | Key Field | Description |
|------|-----------|-------------|
| [Multivalue](multivalue.md) | `multivalue: true` | Extract all key-value pairs from a cloud secret |
| [Single Value](single-value.md) | (default) | Map one remote secret to one K8s secret key |
| [TLS Certificate](tls.md) | `type: "kubernetes.io/tls"` | Split cert and key from separate remote secrets |
| [ArgoCD Cluster](argocd-clusters.md) | `argocd: true` | Register clusters in ArgoCD (cert or bearer token) |
| [ArgoCD Repo Creds](argocd-repo-creds.md) | `argocdRepoCreds: true` | Repository credentials for ArgoCD |
| [Grafana Contact Point](grafana.md) | `contactpoint: true` | Contact point config with `grafana_alert` label |
| [Custom Labels & Types](custom.md) | `type:` / `labels:` | Non-opaque types, custom labels |

## How Types Are Determined

The template uses a priority-based decision tree:

1. **Custom type set** (not TLS) → Non-opaque secret with template
2. **TLS type** → Fetches separate `-crt` and `-key` remote secrets
3. **Contact point** → Grafana alert contact point
4. **ArgoCD repo creds** → Repository credential secret
5. **ArgoCD cluster** → Cluster registration secret (cert or bearer token)
6. **Multivalue** → `dataFrom.extract` — pulls all keys
7. **Property set** → Single key extraction from JSON
8. **Default** → Single value mapping

!!! tip "Combine with Labels"
    Most secret types support the optional `labels` field to attach custom metadata to the generated Kubernetes Secret.
