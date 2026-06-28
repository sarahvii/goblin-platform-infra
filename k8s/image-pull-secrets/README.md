# Image Pull Secrets

Application images are expected to be pulled from a container registry.

Each runtime namespace needs access to the expected image pull secret.

Current namespaces:

* `goblin-int`
* `goblin-pre`
* `goblin-prd`

## Current bootstrap approach

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

## Future options

Later, replace manual secret bootstrap with one of:

* Sealed Secrets
* External Secrets
* SOPS-encrypted manifests
* another GitOps-friendly secret management pattern

Do not commit raw registry credentials to Git.
