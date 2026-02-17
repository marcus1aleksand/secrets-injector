# 🔑 AWS Secrets Manager

## Authentication via IRSA

The AWS provider uses [IAM Roles for Service Accounts (IRSA)](https://docs.aws.amazon.com/eks/latest/userguide/iam-roles-for-service-accounts.html) to authenticate:

```yaml
clustersecretstore:
  name: cluster-aws-backend
  providerType: aws
  aws:
    region: "us-east-1"
    auth:
      serviceAccountName: "external-secrets-sa"
      serviceAccountNamespace: "external-secrets"
```

## Prerequisites

### 1. Create an IAM Policy

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "secretsmanager:GetSecretValue",
        "secretsmanager:DescribeSecret"
      ],
      "Resource": "arn:aws:secretsmanager:us-east-1:123456789:secret:*"
    }
  ]
}
```

### 2. Create an IAM Role with IRSA Trust

```bash
eksctl create iamserviceaccount \
  --name external-secrets-sa \
  --namespace external-secrets \
  --cluster my-cluster \
  --attach-policy-arn arn:aws:iam::123456789:policy/ExternalSecretsPolicy \
  --approve
```

### 3. Verify the Service Account

```bash
kubectl get sa external-secrets-sa -n external-secrets -o yaml
# Should have annotation: eks.amazonaws.com/role-arn
```

## Storing Secrets in AWS

### Single Value

```bash
aws secretsmanager create-secret \
  --name my-secret \
  --secret-string "my-value"
```

### Multivalue (JSON)

```bash
aws secretsmanager create-secret \
  --name app-config \
  --secret-string '{"DB_HOST":"db.example.com","DB_PORT":"5432"}'
```

## Multi-Region

Deploy multiple ClusterSecretStores for different regions by installing the chart multiple times:

```bash
helm install secrets-us-east \
  oci://ghcr.io/marcus1aleksand/helm-charts/secrets-injector \
  -f values-us-east.yaml

helm install secrets-eu-west \
  oci://ghcr.io/marcus1aleksand/helm-charts/secrets-injector \
  -f values-eu-west.yaml
```
