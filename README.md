# org-workflows

Reusable GitHub Actions workflows for the gr8monk3ys fleet. Callers reference
them as:

    uses: gr8monk3ys/org-workflows/.github/workflows/reusable-<name>.yml@<sha>

This repo is public on purpose: reusable workflows in a private repository
cannot be resolved by other private repositories on this plan — every private
caller gets `startup_failure` with no error surfaced anywhere. These files
carry generic CI logic only; nothing here is secret. History: they lived in
gr8monk3ys/github until that repo went private on 2026-07-29, which silently
broke every org-* workflow in every private repo for three weeks.
