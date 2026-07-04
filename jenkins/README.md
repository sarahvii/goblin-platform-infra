# Shared Jenkins runtime

This directory owns the shared Jenkins runtime for the Goblin platform.

Application repositories continue to own application-specific pipeline definitions, deployment scripts, release checks, and application Kubernetes resources.

## Current migration status

This runtime definition is staged for a controlled ownership migration from `goblin-observatory/infra/jenkins`.

Do not switch the live Jenkins service to this Compose file until the preflight checks below have been completed.

The migration must preserve:

- container name `goblin-jenkins`
- host ports `8081` and `50000`
- Jenkins URL prefix `/jenkins`
- existing Jenkins home data
- existing credentials, plugins, jobs, and history
- current Observatory repository access required by live jobs

Kubernetes client tooling is intentionally out of scope here and is tracked separately.

## Why the Jenkins home volume is explicit

Moving a Compose file can change the Compose project name. A Compose-managed named volume may therefore resolve to a different Docker volume after the file moves to another repository.

To prevent Jenkins accidentally starting with a fresh empty home, this Compose file requires the existing Docker volume name to be supplied explicitly through `JENKINS_HOME_VOLUME`.

## Preflight checks on the Jenkins host

### 1. Confirm the current container

```bash
docker ps --filter name=goblin-jenkins
```

### 2. Discover the mounted Jenkins home volume

```bash
docker inspect goblin-jenkins \
  --format '{{range .Mounts}}{{if eq .Destination "/var/jenkins_home"}}{{.Name}}{{end}}{{end}}'
```

Record the returned volume name. Do not guess it.

### 3. Inspect all current mounts

```bash
docker inspect goblin-jenkins \
  --format '{{range .Mounts}}{{println .Type .Source "->" .Destination}}{{end}}'
```

This is required because the repository definition mounts Observatory at `/workspace/goblin-observatory`, while the currently configured live jobs have been observed using `/home/sarah/goblin-observatory`.

Do not cut over until that difference is understood.

### 4. Record current container settings

```bash
docker inspect goblin-jenkins > /tmp/goblin-jenkins-before-migration.json
```

Keep this local. Do not commit the inspection output because it may contain host-specific details.

## Validate the staged Compose definition

Set the discovered existing volume name:

```bash
export JENKINS_HOME_VOLUME=<existing-volume-name>
```

Optionally set host paths when they differ from defaults:

```bash
export GOBLIN_OBSERVATORY_REPO=<host-path-to-goblin-observatory>
export GOBLIN_DOCKER_CONFIG=<host-path-to-docker-config>
```

Render the effective configuration without starting anything:

```bash
docker compose -f jenkins/docker-compose.yml config
```

## Cutover rule

Do not run `docker compose up` from this repository until:

1. the existing Jenkins home volume name is confirmed
2. current live mounts are confirmed
3. the live job repository path mismatch is resolved or deliberately preserved
4. rollback steps are ready

## Rollback

The original runtime definition should remain available in `goblin-observatory` until the new ownership location has been proven on the live host.

If cutover fails:

1. stop the newly managed container without deleting the Jenkins home volume
2. restore the previous runtime command from the Observatory repository
3. confirm `goblin-jenkins` starts with the same Jenkins home volume
4. verify jobs and history before continuing

Never use `docker compose down -v` during this migration.

## Related decisions

See `docs/architecture/decisions/` for the Jenkins topology and ownership decisions.
