# 🏗️ Architecture

## Overview

The Secrets Injector is a Helm chart that generates [External Secrets Operator](https://external-secrets.io/) custom resources. It doesn't run any pods itself — it's purely a declarative layer that templates `ClusterSecretStore` and `ClusterExternalSecret` resources from your `values.yaml`.

## Full Resource Flow

```mermaid
graph TB
    subgraph User["👤 Platform Engineer"]
        VALUES["📝 values.yaml<br/>Single source of truth"]
    end

    subgraph Helm["📦 Secrets Injector Chart"]
        direction TB
        TPL_CSS["Template: clustersecretstore.yaml"]
        TPL_CES["Template: clusterexternalsecrets.yaml"]
    end

    subgraph K8s_Cluster["☸️ Kubernetes Cluster"]
        direction TB

        subgraph Cluster_Scoped["🌐 Cluster-Scoped Resources"]
            CSS["ClusterSecretStore<br/>Provider authentication config"]
            CES["ClusterExternalSecret<br/>One per secret definition"]
        end

        subgraph ESO["⚙️ External Secrets Operator"]
            CTRL["ESO Controller<br/>Watches & reconciles"]
        end

        subgraph NS_Scoped["📁 Namespace-Scoped Resources"]
            ES1["ExternalSecret<br/>(auto-created by CES)"]
            ES2["ExternalSecret<br/>(auto-created by CES)"]
            S1["🔒 K8s Secret<br/>namespace-a"]
            S2["🔒 K8s Secret<br/>namespace-b"]
        end
    end

    subgraph Providers["☁️ Secret Providers"]
        AKV["🔑 Azure Key Vault"]
        ASM["🔑 AWS Secrets Manager"]
        HCV["🔑 HashiCorp Vault"]
    end

    VALUES -->|"helm install/upgrade"| TPL_CSS
    VALUES -->|"helm install/upgrade"| TPL_CES
    TPL_CSS -->|"creates"| CSS
    TPL_CES -->|"creates"| CES
    CSS -.->|"auth config"| CTRL
    CES -->|"spawns per namespace"| ES1
    CES -->|"spawns per namespace"| ES2
    CTRL -->|"watches"| ES1
    CTRL -->|"watches"| ES2
    CTRL <-->|"fetch secrets"| AKV
    CTRL <-->|"fetch secrets"| ASM
    CTRL <-->|"fetch secrets"| HCV
    CTRL -->|"creates/updates"| S1
    CTRL -->|"creates/updates"| S2

    style User fill:#f3e5f5,stroke:#9C27B0,stroke-width:2px
    style Helm fill:#e3f2fd,stroke:#2196F3,stroke-width:2px
    style Cluster_Scoped fill:#e8f5e9,stroke:#4CAF50,stroke-width:2px
    style ESO fill:#fff3e0,stroke:#FF9800,stroke-width:2px
    style NS_Scoped fill:#fce4ec,stroke:#E91E63,stroke-width:2px
    style Providers fill:#e0f2f1,stroke:#009688,stroke-width:2px
```

## Resource Hierarchy

```mermaid
graph LR
    CSS["ClusterSecretStore"] -->|referenced by| CES["ClusterExternalSecret"]
    CES -->|creates in target namespace| ES["ExternalSecret"]
    ES -->|reconciled into| SEC["K8s Secret"]

    style CSS fill:#e8f5e9,stroke:#4CAF50
    style CES fill:#e3f2fd,stroke:#2196F3
    style ES fill:#fff3e0,stroke:#FF9800
    style SEC fill:#fce4ec,stroke:#E91E63
```

| Resource | Scope | Created By | Purpose |
|----------|-------|------------|---------|
| `ClusterSecretStore` | Cluster | Helm chart | Defines how to connect to the cloud provider |
| `ClusterExternalSecret` | Cluster | Helm chart | Defines which secret to fetch and where to put it |
| `ExternalSecret` | Namespace | ESO (from CES) | Namespace-scoped copy, drives reconciliation |
| `Secret` | Namespace | ESO Controller | The actual Kubernetes Secret with synced data |

## Secret Type Decision Flow

```mermaid
flowchart TD
    START["New externalsecret entry<br/>in values.yaml"] --> TYPE{type set?}

    TYPE -->|"kubernetes.io/tls"| TLS["TLS Secret<br/>Fetches -crt and -key"]
    TYPE -->|"other type"| NONOPAQUE["Non-Opaque Secret<br/>Custom type with template"]
    TYPE -->|"not set"| ARGOCD{argocd?}

    ARGOCD -->|true| BEARER{argocdBearerToken?}
    BEARER -->|true| ARGOCD_BT["ArgoCD Cluster<br/>(Bearer Token)"]
    BEARER -->|false| ARGOCD_CERT["ArgoCD Cluster<br/>(Certificate)"]

    ARGOCD -->|false| REPOCREDS{argocdRepoCreds?}
    REPOCREDS -->|true| REPO["ArgoCD Repo Creds<br/>(type, url, user, pass)"]
    REPOCREDS -->|false| CONTACTPT{contactpoint?}

    CONTACTPT -->|true| GRAFANA["Grafana Contact Point<br/>(grafana_alert label)"]
    CONTACTPT -->|false| MULTI{multivalue?}

    MULTI -->|true| EXTRACT["Multivalue Extract<br/>(dataFrom.extract)"]
    MULTI -->|false| PROP{property set?}

    PROP -->|yes| JSONPROP["JSON Property<br/>(single key from JSON)"]
    PROP -->|no| SINGLE["Single Value<br/>(one key → one value)"]

    style START fill:#e3f2fd,stroke:#2196F3
    style TLS fill:#fce4ec,stroke:#E91E63
    style ARGOCD_BT fill:#e8f5e9,stroke:#4CAF50
    style ARGOCD_CERT fill:#e8f5e9,stroke:#4CAF50
    style REPO fill:#e8f5e9,stroke:#4CAF50
    style GRAFANA fill:#fff3e0,stroke:#FF9800
    style EXTRACT fill:#f3e5f5,stroke:#9C27B0
    style JSONPROP fill:#e0f2f1,stroke:#009688
    style SINGLE fill:#e0f2f1,stroke:#009688
```

## Multi-Cloud Support

The `ClusterSecretStore` template supports three providers. Only one is active per store, but you can create multiple stores for multi-cloud setups.

```mermaid
graph TB
    subgraph Stores["Multiple ClusterSecretStores"]
        AZURE_CSS["ClusterSecretStore<br/>azure-backend<br/>(providerType: azurekv)"]
        AWS_CSS["ClusterSecretStore<br/>aws-backend<br/>(providerType: aws)"]
        VAULT_CSS["ClusterSecretStore<br/>vault-backend<br/>(providerType: vault)"]
    end

    subgraph Secrets["ClusterExternalSecrets"]
        S1["app-secret-1<br/>clustersecstore: azure-backend"]
        S2["app-secret-2<br/>clustersecstore: aws-backend"]
        S3["app-secret-3<br/>clustersecstore: vault-backend"]
    end

    AZURE_CSS --> S1
    AWS_CSS --> S2
    VAULT_CSS --> S3

    style Stores fill:#e3f2fd,stroke:#2196F3,stroke-width:2px
    style Secrets fill:#e8f5e9,stroke:#4CAF50,stroke-width:2px
```

!!! info "Multiple Helm Releases"
    To use multiple cloud providers, install the chart multiple times with different release names and `values.yaml` files — one per provider.

## Sync Lifecycle

1. **Helm Install/Upgrade** — Chart templates render `ClusterSecretStore` + `ClusterExternalSecret` resources
2. **CES → ES** — ESO creates a namespace-scoped `ExternalSecret` in each matched namespace
3. **ES → Provider** — ESO controller authenticates via `ClusterSecretStore` and fetches the remote secret
4. **ES → Secret** — ESO creates/updates the Kubernetes `Secret` with the fetched data
5. **Refresh** — Every 5 minutes (configurable), ESO re-fetches and updates the secret
6. **Helm Uninstall** — All resources are cleaned up (secrets have `creationPolicy: Owner`)
