# Reusable GitHub Actions workflows

Shared CI for the whatever413y repos (the M18 Residences apps, server and shared packages). Call a workflow
from a repo's own workflow and pin the major tag:

```yaml
jobs:
  check:
    uses: whatever413y/.github/.github/workflows/<workflow>.yml@v1
```

| Workflow | For | Inputs |
|---|---|---|
| `flutter-package-check.yml` | a Dart/Flutter package: format, analyze, test | `working-directory` (default `.`), `format-paths` (default `lib test`) |
| `flutter-web-app-check.yml` | a Flutter web app: format, analyze, tests if any, release web build | `api-url` (compiled into the check build) |
| `rust-worker-ci.yml` | a Rust Cloudflare Worker: fmt, clippy (native + wasm32), tests, `worker-build` | — |

Everything uses the latest stable Flutter and Rust.

## Releasing

Work on `development`, merge to `main`, then tag: `v1.x.y` plus the moving major tag `v1`
(`git tag -f v1 && git push -f origin v1`). Callers follow `v1`, so a change to `v1` reaches every repo at
once: test it first by pointing one caller at `@development`. A breaking change gets a new major (`v2`).
