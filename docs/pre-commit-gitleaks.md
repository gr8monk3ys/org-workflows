# gitleaks as a pre-commit hook

`reusable-gitleaks.yml` catches a secret after it is pushed. A pre-commit
hook catches it before the commit exists, which is the only point at which
removing it is free (no history rewrite, no rotation).

This repo does not publish a shared `.pre-commit-config.yaml`;
`reusable-precommit.yml` runs whatever config the consumer repo carries.
Add this block to the consumer's `.pre-commit-config.yaml`:

```yaml
repos:
  - repo: https://github.com/gitleaks/gitleaks
    rev: v8.30.1
    hooks:
      - id: gitleaks
```

Then install it once per clone:

```sh
pre-commit install
```

The hook's entry (defined upstream in gitleaks' `.pre-commit-hooks.yaml`) is

```
gitleaks git --pre-commit --redact --staged --verbose
```

which scans only the staged diff. `gitleaks protect --staged` is the pre-8.19
spelling of the same thing and still works, but is deprecated.

Notes:

- `rev` is a tag. Bump it alongside `GITLEAKS_VERSION` in
  `reusable-gitleaks.yml` so the hook and CI agree on the ruleset.
  `pre-commit autoupdate` will do it.
- `language: golang` builds the binary from source on first run, which needs
  Go on the machine. If Go is not installed, use `id: gitleaks-system` instead
  (expects `gitleaks` on `PATH`, e.g. `brew install gitleaks`) or
  `id: gitleaks-docker`.
- A root `.gitleaks.toml` / `.gitleaksignore` applies to the hook exactly as
  it does to CI. To silence a fixture, add its fingerprint (printed in the
  finding) to `.gitleaksignore` rather than loosening the config.
- `reusable-precommit.yml` will run this hook in CI too when present, which
  makes it a second, staged-diff-shaped scan. That is fine; it is cheap.
