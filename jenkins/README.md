# Shared Jenkins runtime

This directory owns the shared Jenkins runtime for the Goblin platform.

Application repositories continue to own application-specific pipelines, deployment scripts, release checks, and application Kubernetes resources.

## Migration status

This runtime is staged for a controlled ownership move from `goblin-observatory/infra/jenkins`.

Do not switch the live Jenkins service to this Compose file until the checks below are complete.

The migration must preserve:

- container name `goblin-jenkins`
- host ports `8081` and `50000`
- Jenkins URL prefix `/jenkins`
- existing Jenkins home data
- existing credentials, plugins, jobs, and history
- Observatory repository access required by current jobs

Kubernetes client tooling is tracked separately.

## Confirmed live settings

Inspection of the running container confirmed:

- image `goblin-jenkins:local`
- container name `goblin-jenkins`
- existing Docker volume mounted at `/var/jenkins_home`
- host port `8081` mapped to container port `8080`
- host port `50000` mapped to container port `50000`
- `JENKINS_OPTS=--prefix=/jenkins`
- Observatory is mounted at the same host-specific path inside the container
- current jobs use that container path

Exact local paths and the existing volume name must be supplied locally and are not committed here.

## Required local configuration

Copy the example:

```bash
cp jenkins/.env.example jenkins/.env
```

Set:

```text
JENKINS_HOME_VOLUME=
GOBLIN_OBSERVATORY_REPO=
GOBLIN_OBSERVATORY_CONTAINER_PATH=
GOBLIN_DOCKER_CONFIG=
```

`GOBLIN_OBSERVATORY_CONTAINER_PATH` must match the path used by current Jenkins jobs.

Do not commit `jenkins/.env`.

## Validate without starting Jenkins

```bash
docker compose --env-file jenkins/.env -f jenkins/docker-compose.yml config
```

Confirm the rendered configuration preserves:

1. the existing Jenkins home volume
2. the Observatory source path
3. the Observatory container destination used by current jobs
4. read-only Docker config at `/root/.docker`
5. ports `8081:8080` and `50000:50000`
6. the `/jenkins` prefix

## Cutover rule

Do not run the new Compose definition until:

1. local values are populated from the live host
2. the rendered configuration has been reviewed
3. rollback steps are ready
4. current jobs and history have been checked

## Rollback

Keep the original runtime definition in `goblin-observatory` until the new ownership location has been proven.

If cutover fails:

1. stop the newly managed container without deleting the Jenkins home volume
2. restore the previous runtime command from the Observatory repository
3. confirm `goblin-jenkins` starts with the same Jenkins home volume
4. verify jobs and history

Never use `docker compose down -v` during this migration.

## Related decisions

See `docs/architecture/decisions/`.
