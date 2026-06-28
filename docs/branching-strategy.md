# Branching Strategy

Goblin repositories use a simple pull-request based workflow.

## Branch model

```text
main
= stable, reviewed, deployable source of truth

<issue-number>-short-description
= short-lived issue or feature branch
```

Examples:

```text
25-branching-strategy
26-review-jenkins-promotion
19-release-metadata-drift
```

## Rules

Routine changes should not be pushed directly to `main`.

Changes should normally follow this flow:

```text
create issue
→ create branch from main
→ make change
→ open pull request
→ run checks
→ review diff
→ merge to main
```

## Main branch

`main` should represent the stable source of truth for the repository.

For platform infrastructure repositories such as Goblin Platform Infra, `main` should represent reviewed and successfully applied infrastructure state.

## Pull requests

Pull requests should include:

* a clear title
* a short summary of the change
* related issue number where relevant
* evidence of testing or validation where relevant

## Copilot review

GitHub Copilot Code Review may be used as an advisory reviewer.

Copilot review can help spot obvious issues in:

* shell scripts
* Jenkinsfiles
* GitHub Actions workflows
* Kubernetes overlays
* release metadata changes
* documentation
* future Terraform changes

Copilot review does not replace CI checks, tests, or deliberate human/self-review.

## Branch protection

The `main` branch should be protected.

Recommended settings:

* require a pull request before merging
* require status checks to pass where checks exist
* do not allow force pushes
* do not allow branch deletion

For solo-maintainer repositories, required approvals may be kept lightweight, but direct pushes to `main` should still be avoided.
