# 🔧 Troubleshooting

## Common Issues

### ClusterSecretStore Not Ready

```bash
kubectl get clustersecretstore
```

**Symptoms:** Status shows `Invalid` or not ready.

**Common causes:**

- Wrong `providerType` value
- Invalid credentials (tenant ID, vault URL, identity ID)
- Managed Identity doesn't have access to the Key Vault
- Service Principal K8s Secret doesn't exist

**Fix:** Check the ESO controller logs:

```bash
kubectl logs -n external-secrets -l app.kubernetes.io/name=external-secrets
```

### ExternalSecret Shows SecretSyncedError

```bash
kubectl get externalsecret -A
kubectl describe externalsecret <name> -n <namespace>
```

**Common causes:**

- Secret doesn't exist in the cloud provider
- Secret name mismatch between `keyvaultsecretname` and the actual cloud secret
- For TLS: missing `-crt` or `-key` suffixed secrets
- For ArgoCD/multivalue: JSON parsing errors in the cloud secret

### Secret Not Created in Namespace

**Check the ClusterExternalSecret:**

```bash
kubectl get clusterexternalsecret <name>
kubectl describe clusterexternalsecret <name>
```

**Common causes:**

- Target namespace doesn't exist
- Namespace label doesn't match when using `namespaceSelector`
- Namespace name mismatch in `namespace` field

### Helm Install Fails

**Lint first:**

```bash
helm lint ./chart -f values.yaml
```

**Common causes:**

- Missing required values (`secret`, `clustersecstore`, `namespacesecretname`, `keyvaultsecretname`)
- Invalid YAML syntax
- Missing `namespacesecretkeyname` for single-value secrets
- Missing `namespacesecretkeynamecrt`/`namespacesecretkeynamekey` for TLS secrets

### Secrets Not Refreshing

Secrets refresh on two intervals:

- **CES refresh:** Every 1 minute (checks for namespace changes)
- **ES refresh:** Every 5 minutes (re-fetches from cloud provider)

If secrets are stale after 5+ minutes:

```bash
# Force a reconciliation
kubectl annotate externalsecret <name> -n <namespace> \
  force-sync=$(date +%s) --overwrite
```

## Useful Commands

```bash
# Overview of all resources
kubectl get clustersecretstore,clusterexternalsecret,externalsecret -A

# Check ESO controller logs
kubectl logs -n external-secrets -l app.kubernetes.io/name=external-secrets -f

# Verify a specific secret's content
kubectl get secret <name> -n <namespace> -o jsonpath='{.data}' | jq

# Check Helm release status
helm list -A | grep secrets-injector
helm get values secrets-injector
```

## Getting Help

- [External Secrets Operator Docs](https://external-secrets.io/)
- [ESO Troubleshooting Guide](https://external-secrets.io/latest/guides/troubleshooting/)
- [Open an issue](https://github.com/marcus1aleksand/secrets-injector/issues)
