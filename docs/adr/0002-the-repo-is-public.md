---
status: accepted
date: 2026-11-22
---

# The repo is public

Reusable workflows in a private repository can only be called from repositories in the same organization, and `gr8monk3ys` is a personal account, not an organization. While these lived in a private repo, every `org-*` job across the fleet failed at startup (`startup_failure`, no logs) for a month. A public repo is the only home that resolves from both owners.

## Consequences

- Nothing secret may live here; secrets reach workflows through the caller.
- Because a reusable workflow runs with the caller's permissions, callers must pin by full commit SHA so a later push cannot change what every consumer executes.
