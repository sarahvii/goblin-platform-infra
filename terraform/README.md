# Terraform

Placeholder for future cloud infrastructure.

This directory may later manage shared platform resources such as:

* cloud IAM roles and bindings
* service accounts
* data warehouse datasets and tables
* object storage buckets
* container registry resources
* Terraform remote state storage

## Current status

No Terraform resources are defined in the first pass.

The initial scope of this repository is Kubernetes platform infrastructure for shared runtime environments.

## Future direction

Terraform may later be used to provision shared cloud resources that support Goblin applications and platform services.

Examples could include:

* shared service accounts
* environment-specific IAM bindings
* datasets and tables for platform or app data
* object storage for artifacts, exports, or Terraform state
* registry or artifact storage resources

## Security note

Do not commit:

* Terraform state files
* real `.tfvars` files
* cloud service account keys
* credentials
* secrets
* local backend configuration containing sensitive values

Use `.tfvars.example` files for public examples if needed.
