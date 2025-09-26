# Renovate Runner (Account-wide)

This repository hosts a GitHub Actions workflow that runs [Renovate](https://github.com/renovatebot/renovate) daily and can also be triggered manually. It autodiscovers repositories in your GitHub account (or org) and opens PRs to keep dependencies up to date.

## Setup

1. Create a Personal Access Token (classic) and store it as a repository secret named `RENOVATE_TOKEN`.
   - Recommended scopes: `repo`, `workflow`, `read:org` (for private repos across the account/org).
   - For org-wide access, ensure the token has access to that org and that organization policies allow GitHub Actions to use secrets.
2. Optional: Restrict which repositories Renovate scans by setting `RENOVATE_AUTODISCOVER_FILTER` in the workflow env or as a repository secret. Example:
   - `owner:your-username/*`
   - `owner:your-org/*`
3. Review/adjust defaults in `pre-sets/default.json` (base preset) and `pre-sets/cdsre.json` (org preset), plus `renovate.json`.

## How it runs

- Scheduled daily at 03:00 UTC.
- Can be run manually: Actions tab -> "Renovate Runner" -> "Run workflow".
- Uses `renovatebot/github-action` with autodiscovery enabled to find all repositories accessible to the token.

## Shared presets (default and cdsre)

- Base preset: `pre-sets/default.json` contains the main Renovate configuration used across projects.
- Org/Team preset: `pre-sets/cdsre.json` extends the base via `local>pre-sets/default` and adds CDSRE-specific tweaks (e.g., extra labels).
- This repository's `renovate.json` extends `local>pre-sets/cdsre` so the runner uses the CDSRE rules.

## Onboarding repos to use the cdsre preset

This runner is configured to create onboarding PRs in target repos with a `renovate.json` that extends the hosted preset in this repository.

- Ensure `renovate.json`'s `onboardingConfig.extends` points to the correct hosted preset path:
  - `github>OWNER/renovate-runner//pre-sets/cdsre.json`
- Replace `OWNER` with your GitHub username or organization name which owns this repository.

The workflow has `RENOVATE_ONBOARDING=true` and `RENOVATE_REQUIRE_CONFIG=true` so that:
- Repos without a Renovate config will receive an onboarding PR.
- The onboarding PR proposes a `renovate.json` that extends the `cdsre` preset.

## Notes

- Automerge is disabled globally in the base preset. Renovate will not auto-merge PRs of any type. If you want to enable automerge for a specific repo, add a per-repo renovate.json with appropriate packageRules.
- Grouping: All minor and patch updates will be batched into a single PR named "all non-major dependencies". Major updates remain separate PRs (and multiple majors are separated by default due to `:separateMultipleMajorReleases`).
- Safety limits are configured via `RENOVATE_PR_HOURLY_LIMIT` and `RENOVATE_PR_CONCURRENT_LIMIT`.
- Add or remove managers and rules in `pre-sets/cdsre.json` to centrally change behavior across all onboarded repositories.
