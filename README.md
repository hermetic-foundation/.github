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
```

The reusable workflow runs on `vars.CI_RUNNER_LABELS`, builds every
`packages.${system}` output, builds every `checks.${system}` output, runs
`nix flake check`, and optionally signs and uploads built store paths to the
Monarchic Nix binary cache. It is intended to run for trusted pushes to `main`,
including merges, and for manual dispatches.
