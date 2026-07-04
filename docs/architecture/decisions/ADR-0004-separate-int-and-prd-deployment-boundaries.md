# ADR-0004: Separate INT and PRD deployment boundaries

## Status

Accepted

## Date

2026-07-04

## Context

Jenkins will interact with Kubernetes as a shared platform client.

INT and PRD have different purposes and risk profiles. INT supports validation before promotion. PRD is the live environment and may require additional release operations such as rollback.

Using one broad Jenkins access model for both environments would weaken separation and increase the impact of pipeline errors.

## Decision

Use separate Jenkins deployment identities and separately scoped Kubernetes permissions for INT and PRD.

Permissions will be based on the actual deployment operations required in each environment.

## Alternatives considered

### One shared deployment identity

Advantages:

- simpler setup
- fewer bindings to maintain

Disadvantages:

- larger blast radius
- weaker environment separation
- harder to express different PRD needs

### Separate environment-specific identities

Advantages:

- clearer boundaries
- permissions can differ by environment
- behaviour can be tested independently

Disadvantages:

- more configuration to maintain
- more validation and documentation required

## Consequences

### Positive

- INT and PRD deployment capabilities are independently controlled.
- PRD-specific operations do not need to be available in INT.
- future environment separation is easier.

### Negative and trade-offs

- Jenkins must select the correct environment-specific configuration.
- access definitions must be maintained for both namespaces.
- deployment script changes may require corresponding access reviews.

## Related

- Issue: #6
- ADR: `ADR-0003-use-kubernetes-client-model-for-jenkins.md`
