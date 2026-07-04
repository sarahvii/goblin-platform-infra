# Shared Jenkins runtime

This directory owns the shared Jenkins runtime for the Goblin platform.

Application repositories continue to own application-specific pipelines, deployment scripts, release checks, and application Kubernetes resources.

## Current state

The live Jenkins container is managed from this repository.

The runtime preserves:

- container name `goblin-jenkins`
- host ports `8081` and `50000`
- Jenkins URL prefix `/jenkins`
- existing Jenkins home data
- existing credentials, plugins, jobs, and history
- Observatory repository access required by current jobs

Kubernetes client tooling is tracked separately.

## Image definition

`jenkins/Dockerfile` defines the custom Jenkins image used by this runtime.

The image currently:

- starts from `jenkins/jenkins:lts-jdk17`
- installs Docker CLI tooling
- installs the Docker Compose plugin
- returns to the Jenkins user after package installation

The Compose service builds from this Dockerfile and tags the result as:

```text
goblin-jenkins:local
```

## Local runtime configuration

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

## Validate configuration without starting Jenkins

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

## Build the platform-owned image without restarting Jenkins

Record the current running image first:

```bash
docker inspect goblin-jenkins --format '{{.Image}}'
```

Build the image from this repository:

```bash
docker compose \
  --env-file jenkins/.env \
  -f jenkins/docker-compose.yml \
  build jenkins
```

Do not run `up` immediately after the build.

Inspect the newly tagged image:

```bash
docker image inspect goblin-jenkins:local --format '{{.Id}}'
```

Validate required tooling without touching the live container:

```bash
docker run --rm goblin-jenkins:local docker version --client
```

```bash
docker run --rm goblin-jenkins:local docker compose version
```

Only recreate the live service after reviewing the new image and preparing rollback.

## Recreate the runtime

```bash
docker compose \
  --env-file jenkins/.env \
  -f jenkins/docker-compose.yml \
  up -d --force-recreate
```

After recreate, verify:

- Jenkins login returns HTTP 200
- existing jobs remain present
- job configuration and build history remain intact
- the existing Jenkins home volume is still mounted
- Compose metadata points to this repository

## Rollback

Do not use `docker compose down -v`.

Keep the previous image ID recorded before rebuilding. If the new image fails validation, retag the previous image ID as `goblin-jenkins:local` and recreate the service from this repository using the same external Jenkins home volume.

## Related decisions

See `docs/architecture/decisions/`.
