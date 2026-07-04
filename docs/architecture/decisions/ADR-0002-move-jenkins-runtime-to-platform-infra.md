# ADR-0002: Move Jenkins runtime ownership to platform infrastructure

## Status

Accepted

## Date

2026-07-04

## Context

The Jenkins runtime currently lives in the Observatory application repository.

Jenkins is evolving into shared delivery infrastructure for Goblin environments. The platform infrastructure repository already owns shared infrastructure and environment provisioning, while application repositories own app-specific release behaviour.

## Decision

Move the Jenkins runtime into `goblin-platform-infra`.

The platform repository owns:

- Jenkins image definition
- Jenkins Compose runtime
- Jenkins runtime documentation
- shared Kubernetes client capability

Application repositories own:

- app-specific Jenkinsfiles
- app-specific deployment scripts
- app-specific Kubernetes resources
- app-specific release checks

## Alternatives considered

### Keep Jenkins in the Observatory repository

Simple now, but shared capability would remain owned by one application.

### Create a dedicated delivery repository

Clear separation, but adds another repository before current scale requires it.

### Use platform infrastructure

Matches the current repository model and supports future reuse.

## Consequences

### Positive

- clearer repository ownership
- shared Jenkins capability can evolve independently of one application
- Observatory remains a platform consumer

### Negative and trade-offs

- the move must preserve existing Jenkins state and behaviour
- documentation must explain the cross-repository boundary

## Related

- Issue: #4
- ADR: `ADR-0001-keep-jenkins-outside-kubernetes.md`
