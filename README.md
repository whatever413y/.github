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
| `workers-deploy-api.yml` | deploy a Rust Worker, wait for `/health` to report the commit, move the `live` tag | `health-url` |
| `workers-deploy-web.yml` | build a Flutter web app (with the repo variable `API_URL`), deploy it as a static-assets Worker, check `version.txt`, move `live` | `site-url` |
| `d1-migrate.yml` | list or apply a D1 database's migrations in the `production-db` environment (approval) | `database`, `command` (`list` / `apply`) |

The deploy and migrate workflows run in the caller's `production` / `production-db` environments (secret
`CLOUDFLARE_API_TOKEN`, variable `CLOUDFLARE_ACCOUNT_ID`; set up by m18-residences-infra), and the calling job needs
`permissions: contents: write` to move the `live` tag (what is actually deployed).

Everything uses the latest stable Flutter and Rust.

## Releasing

Work on `development`, merge to `main`, then tag: `v1.x.y` plus the moving major tag `v1`
(`git tag -f v1 && git push -f origin v1`). Callers follow `v1`, so a change to `v1` reaches every repo at
once: test it first by pointing one caller at `@development`. A breaking change gets a new major (`v2`).
