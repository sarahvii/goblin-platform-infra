# Architecture Decision Records

This directory records important Goblin platform decisions.

ADRs explain the context, decision, alternatives considered, and consequences.

## When to add an ADR

Add one for significant changes to:

- runtime topology
- repository ownership
- CI/CD architecture
- environment promotion
- cluster integration
- persistence
- major technology choices

## Status values

- Proposed
- Accepted
- Superseded
- Deprecated

When a decision changes materially, add a new ADR and mark the earlier one as superseded.

## Index

| ADR | Status | Decision |
|---|---|---|
| [ADR-0001](ADR-0001-keep-jenkins-outside-kubernetes.md) | Accepted | Keep Jenkins outside Kubernetes |
| [ADR-0002](ADR-0002-move-jenkins-runtime-to-platform-infra.md) | Accepted | Move Jenkins runtime ownership to shared platform infrastructure |
| [ADR-0003](ADR-0003-use-kubernetes-client-model-for-jenkins.md) | Accepted | Use a Kubernetes client model for Jenkins |
| [ADR-0004](ADR-0004-separate-int-and-prd-deployment-boundaries.md) | Accepted | Separate INT and PRD deployment boundaries |

Copy [`ADR-TEMPLATE.md`](ADR-TEMPLATE.md) for new decisions.
