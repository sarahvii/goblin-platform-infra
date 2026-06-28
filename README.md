# Goblin Platform Infra

Shared infrastructure for the Goblin platform.

This repository is the source of truth for infrastructure that supports Goblin applications and runtime environments.

It starts with Kubernetes platform resources and is structured to later include Terraform-managed cloud infrastructure.

## Repository model

```text
goblin-platform-infra
= shared infrastructure and environment provisioning

goblin-platform
= future platform product/control plane

app repos
= application code, app Kubernetes overlays, release metadata, and app release scripts
```

## What belongs here

This repo owns shared infrastructure such as:

* Kubernetes namespaces
* shared Kubernetes RBAC and service accounts
* image pull secret strategy and documentation
* ingress configuration later
* environment allocation documentation
* Terraform for shared cloud resources later

## What does not belong here

Application repos should own:

* application source code
* Dockerfiles
* app-specific Kubernetes Deployment and Service manifests
* app-specific release metadata
* app deploy, promote, and status scripts

## Current Kubernetes environments

| Environment | Namespace    | Purpose                |
| ----------- | ------------ | ---------------------- |
| INT         | `goblin-int` | Integration runtime    |
| PRE         | `goblin-pre` | Pre-production runtime |
| PRD         | `goblin-prd` | Production runtime     |

## Apply Kubernetes platform resources

From a machine with access to the target Kubernetes cluster:

```bash
kubectl apply -k k8s/
```

For a k3s cluster, this may look like:

```bash
sudo k3s kubectl apply -k k8s/
```

## Validate namespaces

```bash
kubectl get namespaces | grep goblin
```

Expected namespaces:

```text
goblin-int
goblin-pre
goblin-prd
```

## Image pull secrets

Goblin application images are expected to be pulled from a container registry.

Each runtime namespace needs access to the expected image pull secret.

Current namespaces:

* `goblin-int`
* `goblin-pre`
* `goblin-prd`

For now, create the image pull secret in each namespace manually during cluster bootstrap.

Use placeholders in documentation only. Do not commit real token values.

Example:

```bash
kubectl create secret docker-registry <pull-secret-name> \
  --docker-server=<registry-host> \
  --docker-username=<registry-username> \
  --docker-password=<registry-token> \
  --namespace=<namespace>
```

Repeat for each runtime namespace.

Future options:

* Sealed Secrets
* External Secrets
* SOPS-encrypted manifests
* another GitOps-friendly secret management pattern

Do not commit raw registry credentials to Git.

## RBAC

Shared Kubernetes RBAC resources may be added later.

This may include:

* deployer service accounts
* read-only status service accounts
* namespace-scoped app deployment permissions
* platform dashboard permissions

## Terraform

Terraform is not implemented in the first pass, but this repository is structured to hold future shared cloud infrastructure.

The `terraform/` directory may later manage resources such as:

* cloud IAM roles and bindings
* service accounts
* data warehouse datasets and tables
* object storage buckets
* container registry resources
* Terraform remote state storage

Do not commit Terraform state or real `.tfvars` files.

## Security note

This repository is public-safe by design.

Do not commit:

* tokens
* passwords
* kubeconfig files
* private SSH keys
* raw Kubernetes Secret manifests containing real values
* Terraform state
* `.tfvars` files containing real values
* cloud service account keys
* personal hostnames, usernames, or local network details

## Branching strategy

Goblin Platform Infra uses a pull-request based branching workflow.

See [docs/branching-strategy.md](docs/branching-strategy.md).
