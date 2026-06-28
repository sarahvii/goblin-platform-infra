# RBAC

Placeholder for shared Kubernetes RBAC resources.

This may later include:

* deployer service accounts
* read-only status service accounts
* namespace-scoped app deployment permissions
* platform dashboard permissions

## Current status

No RBAC resources are defined in the first pass.

Application deployment currently relies on the existing cluster administration workflow.

## Future direction

The platform may later define more specific service accounts and permissions for:

* applying platform infrastructure
* deploying applications into runtime namespaces
* reading release and health status
* supporting a future platform dashboard or control plane

The goal is to move toward least-privilege access as the platform becomes more structured.
