# AGENTS.md

Tooling for a shared GitHub App that Terraform and CI authenticate as. It holds
the App manifest, a script that creates the App via the manifest flow, and a
small Terraform module that manages the installation's repository access.

## What to know

- Terraform can't create a GitHub App (no API). `scripts/create-github-app.sh`
  runs the manifest flow: serve a local form, one approval click in the browser,
  exchange the code, then store `GH_APP_*` credentials (org variables + secret).
- `manifest.json` is the App's declarative config (name, permissions, no
  webhook). Its live permissions live in the App settings, so keep both in sync.
- The Terraform is a module (no provider/backend). It reads the App and adds
  installation-to-repo associations additively, so it won't detach repos other
  projects share on the same App.

## Working here

- The script is bash; run `bash -n` and shellcheck before committing.
- Run `terraform fmt` and `terraform validate` on the module.
- Keep prose plain and human; avoid AI tells.
- Release with semver tags (`v0.1.0`).
