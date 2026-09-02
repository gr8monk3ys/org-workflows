# org-workflows

Reusable GitHub Actions workflows shared by every repository under
`gr8monk3ys` and `Vivancedata`. Each consumer repo carries a thin `org-*.yml`
caller that pins one of these by commit SHA.

## Why this repo is public

Reusable workflows in a **private** repository can only be called from
repositories in the same *organization* — and a personal account is not an
organization. When these lived in a private repo, every `org-*` job across
the fleet died at startup (`startup_failure`, no logs) for a month before the
cause was found. Public is the only home that resolves from both the personal
account and the org.

## Workflows

| Workflow | What it does | Inputs |
|---|---|---|
| `reusable-ci-tests-minimum.yml` | Detects the stack from the files present (Python / Node / Go) and runs its test command. Minimum viable CI for repos with no bespoke pipeline. | — |
| `reusable-codeql.yml` | CodeQL analysis with SARIF upload. | `languages` (default `python,javascript-typescript`), `upload` (`always` \| `failure-only` \| `never`) |
| `reusable-gitleaks.yml` | Secret scan. On `pull_request` scans only the commits the PR adds (`base.sha..head.sha`); on `schedule` / `workflow_dispatch` scans the full history. Honours the repo's `.gitleaks.toml` / `.gitleaksignore`. Pre-commit counterpart: [`docs/pre-commit-gitleaks.md`](docs/pre-commit-gitleaks.md). | — |
| `reusable-trufflehog.yml` | Verified-secret scan. | — |
| `reusable-osv.yml` | OSV dependency-vulnerability scan. | — |
| `reusable-trivy.yml` | Trivy filesystem scan. | — |
| `reusable-precommit.yml` | Runs the repo's `pre-commit` config. | — |
| `reusable-release-please.yml` | release-please PRs and tags. Needs the repo setting *"Allow GitHub Actions to create and approve pull requests"* — it was off in 20 of 22 repos and every run died at PR creation. | — |

## Calling one

```yaml
# .github/workflows/org-codeql.yml in a consumer repo
name: org-codeql
on:
  workflow_dispatch:
  schedule:
    - cron: "23 9 3 * *"   # monthly — see "Minutes" below
permissions:
  actions: read
  contents: read
  security-events: write
jobs:
  codeql:
    uses: gr8monk3ys/org-workflows/.github/workflows/reusable-codeql.yml@2659a9a32c4e6e3858f69a2f502210941a886d5a
    with:
      languages: python
```

**Pin by full commit SHA, never `@main`.** A reusable workflow runs with the
caller's permissions; a tag or branch reference lets a later push change what
every consumer executes. Dependabot's `github-actions` ecosystem bumps SHA
pins like any other action.

## Minutes

Private repos on the free plan share ~2,000 Actions minutes a month; public
repos are unlimited. Consumers therefore run the scanning workflows on a
**monthly schedule plus `workflow_dispatch`**, not on every push — the
"minute-safe policy" comment you will see in every caller. A run that fails
in 2–5 seconds with no logs is exhausted minutes, not a broken workflow.

## Changing a workflow

Edit here, push, then bump the SHA in each caller (Dependabot will open those
PRs). Verify a required check by sampling a **pull-request head**, not the
default branch — check names differ between `push` and `pull_request`
events, and requiring a name that never fires on a PR blocks every merge in
that repo permanently.

GPL-3.0-or-later.
