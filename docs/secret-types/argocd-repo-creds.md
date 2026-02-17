# 📂 ArgoCD Repository Credentials

Create repository credential templates for ArgoCD with the `argocd.argoproj.io/secret-type: repo-creds` label.

## Configuration

```yaml
externalsecrets:
  - secret: github-repo-creds
    argocdRepoCreds: true
    clustersecstore: cluster-azure-backend
    namespace: argocd
    namespacesecretname: github-repo-creds
    keyvaultsecretname: argocd-github-credentials
```

## Required Properties in Cloud Secret

```json
{
  "type": "git",
  "url": "https://github.com/my-org",
  "username": "git-user",
  "password": "ghp_xxxxxxxxxxxx"
}
```

## Generated Secret

The resulting Kubernetes Secret will have:

- **Labels:** `argocd.argoproj.io/secret-type: repo-creds`
- **Keys:** `type`, `url`, `username`, `password`

!!! info "Credential Templates"
    ArgoCD repo-creds are credential **templates**. Any repository whose URL matches the `url` pattern will automatically use these credentials. See [ArgoCD docs](https://argo-cd.readthedocs.io/en/stable/operator-manual/declarative-setup/#repository-credentials).
