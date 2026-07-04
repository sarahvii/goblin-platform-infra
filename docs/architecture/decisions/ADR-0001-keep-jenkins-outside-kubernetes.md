# ADR-0001: Keep Jenkins outside Kubernetes

## Status

Accepted

## Date

2026-07-04

## Context

Goblin application workloads run in k3s on the Raspberry Pi host `goblinpi`.

Jenkins runs on the same host as a standalone Docker service. A decision was required on whether Jenkins itself should move into Kubernetes as the application runtime evolved from Docker Compose to k3s.

## Decision

Keep Jenkins outside Kubernetes as a standalone Docker service.

Jenkins remains release tooling for Kubernetes-hosted applications rather than becoming part of the runtime it manages.

## Alternatives considered

### Run Jenkins inside k3s

Advantages:

- one orchestration model
- potential future use of Kubernetes-managed agents

Disadvantages:

- Jenkins availability would depend on the same single-node cluster it manages
- additional persistence and recovery complexity
- no physical resilience gain because both would still share one Pi

### Keep Jenkins as a Docker service

Advantages:

- preserves a separate runtime boundary
- matches the current working installation
- avoids extra Kubernetes complexity without a clear resilience benefit

Disadvantages:

- Docker and k3s both need operational documentation
- Jenkins requires an explicit integration model for Kubernetes

## Consequences

### Positive

- Kubernetes application failures do not automatically imply Jenkins is part of the same runtime failure.
- The platform can use Docker for delivery tooling and Kubernetes for application workloads deliberately.

### Negative and trade-offs

- Jenkins, INT, and PRD still share the same physical host failure domain.
- Two runtime models must be operated.

## Related

- Issue: #4
- Issue: #5
