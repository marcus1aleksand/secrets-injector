# 📊 Grafana Contact Points

Create secrets for Grafana alert contact points with the `grafana_alert: "true"` label.

## Configuration

```yaml
externalsecrets:
  - secret: grafana-contact-points
    contactpoint: true
    clustersecstore: cluster-azure-backend
    namespace: monitoring
    namespacesecretname: grafana-contact-points
    keyvaultsecretname: grafana-contactpoints-yaml
```

## Cloud Secret Content

Store your contact points YAML as the secret value:

```yaml
apiVersion: 1
contactPoints:
  - orgId: 1
    name: slack-notifications
    receivers:
      - uid: slack-1
        type: slack
        settings:
          url: https://hooks.slack.com/services/xxx
```

## Generated Secret

The resulting Kubernetes Secret will have:

- **Type:** `Opaque`
- **Labels:** `grafana_alert: "true"`
- **Key:** `contactpoints.yaml` containing the YAML content

Grafana's sidecar or provisioning system can then pick up secrets with the `grafana_alert` label and apply the contact point configuration.
