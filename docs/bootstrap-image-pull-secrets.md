# Bootstrap Image Pull Secrets

Application images are pulled from a container registry.

Each runtime namespace needs a pull secret with the expected name before application workloads are deployed.

## Runtime namespaces

* `goblin-int`
* `goblin-pre`
* `goblin-prd`

## Manual bootstrap

Create the pull secret in each namespace using placeholder values:

```bash
kubectl create secret docker-registry <pull-secret-name> \
  --docker-server=<registry-host> \
  --docker-username=<registry-username> \
  --docker-password=<registry-token> \
  --namespace=<namespace>
```

Repeat for each namespace.

## Copying an existing pull secret

If one namespace already has the correct pull secret, it can be copied to another namespace:

```bash
kubectl get secret <pull-secret-name> -n <source-namespace> -o yaml \
  | sed 's/namespace: <source-namespace>/namespace: <target-namespace>/' \
  | kubectl apply -n <target-namespace> -f -
```

## Security

Do not commit:

* real registry tokens
* rendered Secret manifests
* `.dockerconfigjson` values
* kubeconfig files
* local credential files

This bootstrap step is intentionally documented rather than committed as a raw secret.
