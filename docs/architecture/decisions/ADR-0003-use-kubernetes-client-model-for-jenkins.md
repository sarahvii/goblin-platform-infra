# ADR-0003: Use a Kubernetes client model for Jenkins

## Status

Accepted

## Date

2026-07-04

## Context

The application runtime now uses Kubernetes on k3s, while Jenkins still reflects an older Docker-focused release model.

Two main integration options were considered:

1. Jenkins triggers deployment commands through the Pi host.
2. Jenkins interacts with the Kubernetes API as a dedicated client.

## Decision

Use option 2.

Jenkins will remain outside Kubernetes but will act as a Kubernetes API client using explicitly configured client tooling and environment-specific access.

## Alternatives considered

### Execute through the Pi host

Advantages:

- simpler initial setup
- reuses existing host tooling

Disadvantages:

- tighter coupling to host execution
- less portable as the platform grows
- weaker separation between orchestration and host administration

### Use a Kubernetes client model

Advantages:

- explicit platform integration boundary
- supports environment-specific access
- easier to test allowed and denied operations
- better fit for future platform growth

Disadvantages:

- requires Kubernetes client tooling in Jenkins
- requires connectivity and configuration work
- adds initial complexity

## Consequences

### Positive

- Jenkins interaction with Kubernetes becomes explicit and reviewable.
- The model can support separate INT and PRD boundaries.
- The design is more portable if the runtime later moves beyond one Pi.

### Negative and trade-offs

- Jenkins runtime maintenance becomes more complex.
- Connectivity and client configuration must be documented and tested.

## Related

- Issue: #5
- Issue: #6
- ADR: `ADR-0001-keep-jenkins-outside-kubernetes.md`
