# org-workflows

Reusable GitHub Actions workflows shared by every repository under `gr8monk3ys` and `Vivancedata`. Consumers call them; nothing here runs on its own.

## Language

### Sharing

**Reusable workflow**:
A `reusable-*.yml` file in `.github/workflows/` triggered by `workflow_call`. It runs with the caller's permissions.
_Avoid_: shared action, template

**Caller**:
The thin `org-*.yml` workflow in a consumer repo that invokes a Reusable workflow and supplies its inputs.
_Avoid_: consumer workflow, wrapper

**Consumer repo**:
Any repo under `gr8monk3ys` or `Vivancedata` that carries a Caller.

**Pin**:
Referencing a Reusable workflow by full commit SHA, never a branch or tag.
_Avoid_: `@main`, version

### Running

**Minute-safe policy**:
Callers run scanning workflows on a monthly `schedule` plus `workflow_dispatch`, not on every push, to stay inside the free plan's shared Actions minutes.

**Upload mode**:
The `upload` input of `reusable-codeql.yml` (`always`, `failure-only`, `never`). Anything other than `always` keeps the SARIF as the `codeql-sarif` run artifact instead of uploading to code scanning.

**Release token**:
The optional `release-token` secret of `reusable-release-please.yml`, a PAT. Without it the release PR is opened by `GITHUB_TOKEN`, which does not trigger checks on that PR.

### Scanning

**PR scan** / **Full-history scan**:
The two modes of `reusable-gitleaks.yml`. On `pull_request` it scans only the commits the PR adds (`base.sha..head.sha`); on `schedule` / `workflow_dispatch` it scans all history.

**Stack detection**:
How `reusable-ci-tests-minimum.yml` picks its test command (Python, Node or Go) from the files present.
