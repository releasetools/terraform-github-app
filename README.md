# terraform-github-app

Tooling for a shared GitHub App that automates repositories. Create the App from
a manifest, store its credentials, and pin which repos its installation can
reach.

GitHub has no API to create an App, so creation is a one-click manifest flow
(`scripts/create-github-app.sh`). The Terraform here manages the installation's
repository access afterward.

## Create the App

```sh
scripts/create-github-app.sh
```

It asks where the App should live (an organization or your account), runs the
manifest flow in your browser (one click), then stores the credentials. For an
org it sets, on that org:

- `GH_APP_CLIENT_ID` and `GH_APP_ID` (variables), `GH_APP_PRIVATE_KEY` (secret).

A workflow mints a token from these with `actions/create-github-app-token`. The
App's permissions live in `manifest.json`: Administration, Issues, and
Environments write; Contents, Metadata, and Secrets read; plus org Secrets read.

Overrides (skip the prompt): `OWNER=<org>` (or your login for a personal app),
`PORT`, `VISIBILITY`, `MANIFEST`.

## Pin installation access

Install the App on your repos (the script prints the link), then pin that as code
with the module in this repo:

```hcl
provider "github" {
  owner = "your-org"
}

module "app_install" {
  source = "git::https://github.com/releasetools/terraform-github-app.git?ref=v0.1.0"

  app_slug        = "your-app-slug"
  installation_id = "12345678"   # gh api /orgs/<org>/installations
  repositories    = ["repo-a", "repo-b"]
}
```

The association is additive, so it never detaches repos that other projects share
on the same App.

## Requirements

- Terraform >= 1.10, `integrations/github` ~> 6.0
- `gh`, `jq`, and `python3` for the create script
