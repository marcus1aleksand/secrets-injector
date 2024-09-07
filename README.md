# secrets-injector

![Version: 1.0.0](https://img.shields.io/badge/Version-1.0.0-informational?style=flat-square)

The secrets-injector is an add-on tool to the external-secrets operator (https://external-secrets.io/).

The main goal of the secrets-injector tool is to facilitate the creation and management of external-secrets resources in a Kubernetes cluster. It allows users to create external-secrets resources in a declarative way, by defining a variety of secrets with just a few lines of code.

## Helm-chart Description

Secrets Injector for external-secrets operator

## Maintainers

| Name | Email | Url |
| ---- | ------ | --- |
| Marcus Aleksandravicius | <marcus1aleksand@gmail.com> |  |

## Values

<table height="400px" >
	<thead>
		<th>Key</th>
		<th>Type</th>
		<th>Default</th>
		<th>Description</th>
	</thead>
	<tbody>
		<tr>
			<td id="clustersecretstore--azurekv--identityid"><a href="./values.yaml#L10">clustersecretstore.azurekv.identityid</a></td>
			<td>
string
</td>
			<td>
				<div style="max-width: 300px;">
<pre lang="json">
"changeme"
</pre>
</div>
			</td>
			<td></td>
		</tr>
		<tr>
			<td id="clustersecretstore--azurekv--tenantid"><a href="./values.yaml#L6">clustersecretstore.azurekv.tenantid</a></td>
			<td>
string
</td>
			<td>
				<div style="max-width: 300px;">
<pre lang="json">
"changeme"
</pre>
</div>
			</td>
			<td></td>
		</tr>
		<tr>
			<td id="clustersecretstore--azurekv--vaulturl"><a href="./values.yaml#L8">clustersecretstore.azurekv.vaulturl</a></td>
			<td>
string
</td>
			<td>
				<div style="max-width: 300px;">
<pre lang="json">
"changeme"
</pre>
</div>
			</td>
			<td></td>
		</tr>
		<tr>
			<td id="clustersecretstore--name"><a href="./values.yaml#L2">clustersecretstore.name</a></td>
			<td>
string
</td>
			<td>
				<div style="max-width: 300px;">
<pre lang="json">
"cluster-azure-backend"
</pre>
</div>
			</td>
			<td></td>
		</tr>
		<tr>
			<td id="clustersecretstore--providerType"><a href="./values.yaml#L3">clustersecretstore.providerType</a></td>
			<td>
string
</td>
			<td>
				<div style="max-width: 300px;">
<pre lang="json">
"azurekv"
</pre>
</div>
			</td>
			<td></td>
		</tr>
		<tr>
			<td id="externalsecrets[0]--argocd"><a href="./values.yaml#L42">externalsecrets[0].argocd</a></td>
			<td>
bool
</td>
			<td>
				<div style="max-width: 300px;">
<pre lang="json">
false
</pre>
</div>
			</td>
			<td></td>
		</tr>
		<tr>
			<td id="externalsecrets[0]--clustersecstore"><a href="./values.yaml#L46">externalsecrets[0].clustersecstore</a></td>
			<td>
string
</td>
			<td>
				<div style="max-width: 300px;">
<pre lang="json">
"cluster-azure-backend"
</pre>
</div>
			</td>
			<td></td>
		</tr>
		<tr>
			<td id="externalsecrets[0]--keyvaultsecretname"><a href="./values.yaml#L52">externalsecrets[0].keyvaultsecretname</a></td>
			<td>
string
</td>
			<td>
				<div style="max-width: 300px;">
<pre lang="json">
"changeme"
</pre>
</div>
			</td>
			<td></td>
		</tr>
		<tr>
			<td id="externalsecrets[0]--multivalue"><a href="./values.yaml#L44">externalsecrets[0].multivalue</a></td>
			<td>
bool
</td>
			<td>
				<div style="max-width: 300px;">
<pre lang="json">
true
</pre>
</div>
			</td>
			<td></td>
		</tr>
		<tr>
			<td id="externalsecrets[0]--namespace"><a href="./values.yaml#L48">externalsecrets[0].namespace</a></td>
			<td>
string
</td>
			<td>
				<div style="max-width: 300px;">
<pre lang="json">
"changeme"
</pre>
</div>
			</td>
			<td></td>
		</tr>
		<tr>
			<td id="externalsecrets[0]--namespacesecretname"><a href="./values.yaml#L50">externalsecrets[0].namespacesecretname</a></td>
			<td>
string
</td>
			<td>
				<div style="max-width: 300px;">
<pre lang="json">
"changeme"
</pre>
</div>
			</td>
			<td></td>
		</tr>
		<tr>
			<td id="externalsecrets[0]--secret"><a href="./values.yaml#L40">externalsecrets[0].secret</a></td>
			<td>
string
</td>
			<td>
				<div style="max-width: 300px;">
<pre lang="json">
"changeme"
</pre>
</div>
			</td>
			<td></td>
		</tr>
	</tbody>
</table>

## Installation

Install the secrets-injector chart:

```bash
helm install secrets-injector oci://ghcr.io/marcus1aleksand/helm-charts/secrets-injector
```

## Security Checks

Security checks in this repository are performed by a pipeline that executes Checkov whenever a Pull Request is created against the main branch.

[Checkov](https://github.com/bridgecrewio/checkov?tab=readme-ov-file) is a static code analysis tool for infrastructure as code (IaC) and also a software composition analysis (SCA) tool for images and open source packages.

It scans cloud infrastructure provisioned using Terraform, Terraform plan, Cloudformation, AWS SAM, Kubernetes, Helm charts, Kustomize, Dockerfile, Serverless, Bicep, OpenAPI or ARM Templates and detects security and compliance misconfigurations using graph-based scanning.

It performs Software Composition Analysis (SCA) scanning which is a scan of open source packages and images for Common Vulnerabilities and Exposures (CVEs).

Checkov also powers Prisma Cloud Application Security, the developer-first platform that codifies and streamlines cloud security throughout the development lifecycle. Prisma Cloud identifies, fixes, and prevents misconfigurations in cloud resources and infrastructure-as-code files.

## Validation Hooks

This repository has pre-commit hooks configuration within it. This is utilized to run a set of validations locally such as automatically fixing formatting issues before the code is pushed to a remote branch.git s

In order to have the pre-commit working in your local IDE, after cloning this repository locally, run the following commands:

1. Install pre-commit locally
```
brew install pre-commit
```

2. After cloning this repository and having pre-commit installed in your locall computer, run the following command via CLI in the repository directory:
```
pre-commit install
```
Done! now whenever a commit command is executed, your code terraform code will be fully validated and documentation will be automatically updated before it is pushed to the remote repository's branch.

----------------------------------------------
Autogenerated from chart metadata using [helm-docs v1.14.2](https://github.com/norwoodj/helm-docs/releases/v1.14.2)
