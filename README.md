# Monarchic Meta GitHub Defaults

Shared GitHub Actions workflows and organization defaults for Monarchic
repositories.

## Reusable Workflows

### Nix CI

Use `.github/workflows/nix-ci.yml` from repositories with a Nix flake:

```yaml
name: Nix CI

on:
  push:
    branches: [main]
  workflow_dispatch:

jobs:
  nix-ci:
    uses: monarchic-meta/.github/.github/workflows/nix-ci.yml@main
    with:
      publish_cache: ${{ github.event_name == 'push' && github.ref == 'refs/heads/main' }}
    secrets: inherit
```

The reusable workflow runs on `vars.CI_RUNNER_LABELS`, builds every
`packages.${system}` output, builds every `checks.${system}` output, runs
`nix flake check`, and optionally signs and uploads built store paths to the
Monarchic Nix binary cache. It is intended to run for trusted pushes to `main`,
including merges, and for manual dispatches. If the caller repository has a
`MONARCHIC_GITHUB_PAT` secret, the workflow uses it for private flake inputs.

### Release

Use `.github/workflows/release.yml` as a preflight job from repository release
workflows before publishing packages, images, deployments, or GitHub releases:

```yaml
jobs:
  release-preflight:
    uses: monarchic-meta/.github/.github/workflows/release.yml@main
    secrets: inherit
```

The reusable workflow runs on `vars.CI_RUNNER_LABELS`, requires a tag ref,
checks that the tag matches `v*.*.*` by default, verifies that the tag points at
`origin/main`, and verifies that the caller repository already has a successful
`Nix CI` workflow run for the tagged commit. It performs no publishing or
deployment itself; caller workflows keep repo-specific release steps behind
this preflight job.
