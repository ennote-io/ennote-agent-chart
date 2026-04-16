# Ennote Kubernetes Secret Sync Agent

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Artifact Hub](https://img.shields.io/endpoint?url=https://artifacthub.io/badge/repository/ennote)](https://artifacthub.io/packages/search?repo=ennote)
[![Website](https://img.shields.io/badge/Website-Ennote.io-brightgreen.svg)](https://ennote.io)
[![Documentation](https://img.shields.io/badge/Docs-Ennote%20Agent-blue.svg)](https://docs.ennote.io)

A zero-ingress, headless Kubernetes synchronization worker for [Ennote.io](https://ennote.io). This agent securely pulls secrets from your Ennote Cloud and synchronizes them directly into Kubernetes native `Secret` objects within its deployed namespace.

## Security Posture
As a cybersecurity company, this chart is built with least-privilege by default:
* **Zero Ingress:** The agent operates entirely via outbound connections to the Ennote API. No inbound ports are exposed.
* **Strict RBAC:** Permissions are scoped strictly to a `Role` (not a `ClusterRole`), limiting access to a single target namespace.
* **Hardened Pod:** Runs as non-root, drops all capabilities, disables privilege escalation, and utilizes a `readOnlyRootFilesystem`.
* **Configuration Separation:** Non-sensitive configuration is mounted via `ConfigMap`, while the authentication token is isolated as a secure environment variable.
* **Signed Artifacts:** All releases are cryptographically signed via Cosign.

## Prerequisites

* Kubernetes 1.20+
* Helm 3.8.0+ (Required for OCI registry support)
* An active Ennote.io workspace

## Installation

Because this chart is hosted as an OCI artifact on the GitHub Container Registry, you do not need to use `helm repo add`. You can install it directly using the `oci://` URI.

### Option 1: Bootstrap via CLI (Quickstart)
Pass your short-lived (2-hour) bootstrap token directly.

```bash
helm install ennote-agent oci://ghcr.io/ennote-io/charts/ennote-agent \
  --namespace <YOUR_NAMESPACE> \
  --set ennote.auth.token="<YOUR_BOOTSTRAP_TOKEN>"
```

### Option 2: Pre-existing K8s Secret (Production / GitOps Recommended)
For GitOps workflows (like ArgoCD or Flux), do not commit your token to version control. Instead, create a K8s secret first:

```bash
kubectl create secret generic ennote-auth-secret --from-literal=token="<YOUR_BOOTSTRAP_TOKEN>" -n <YOUR_NAMESPACE>
```

Then, deploy the chart referencing that secret:

```bash
helm install ennote-agent oci://ghcr.io/ennote-io/charts/ennote-agent \
  --namespace <YOUR_NAMESPACE> \
  --set ennote.auth.existingSecretName="ennote-auth-secret" \
  --set ennote.auth.existingSecretKey="token"
```

## Verifying Chart Signatures

We sign our OCI artifacts using GitHub OIDC via Sigstore's Cosign. To verify the integrity of the downloaded chart:

```bash
# 1. Pull the chart locally
helm pull oci://ghcr.io/ennote-io/charts/ennote-agent

# 2. Verify the signature against the Ennote GitHub Actions identity
cosign verify \
  --certificate-oidc-issuer="https://token.actions.githubusercontent.com" \
  --certificate-identity-regexp="^https://github.com/ennote-io/.*" \
  ghcr.io/ennote-io/charts/ennote-agent
```

## Configuration Parameters

Below are the primary configuration parameters. For a complete list, view the `values.yaml` file.

| Parameter                        | Description                                                       | Default  |
|----------------------------------|-------------------------------------------------------------------|----------|
| `ennote.k8s.secretPrefix`        | Prefix added to K8s secrets created by the agent                  | `""`     |
| `ennote.k8s.cleanupOrphans`      | Automatically delete K8s secrets if removed from the Ennote Cloud | `false`  |
| `ennote.auth.token`              | Bootstrap token (Not recommended for GitOps)                      | `""`     |
| `ennote.auth.existingSecretName` | Name of existing secret containing the token                      | `""`     |

## Uninstalling the Chart

To uninstall/delete the `ennote-agent` deployment:

```bash
helm uninstall ennote-agent --namespace <YOUR_NAMESPACE>
```

---
*For vulnerability reporting and responsible disclosure, please refer to our [SECURITY.md](SECURITY.md).*