# Renovate Runner (Account-wide)

This repository hosts a GitHub Actions workflow that runs [Renovate](https://github.com/renovatebot/renovate) daily and can also be triggered manually. It autodiscovers repositories in your GitHub account (or org) and opens PRs to keep dependencies up to date.

## Setup

1. Create a Personal Access Token (classic) and store it as a repository secret named `RENOVATE_TOKEN`.
   - Recommended scopes: `repo`, `workflow`, `read:org` (for private repos across the account/org).
   - For org-wide access, ensure the token has access to that org and that organization policies allow GitHub Actions to use secrets.
2. Optional: Restrict which repositories Renovate scans by setting `RENOVATE_AUTODISCOVER_FILTER` in the workflow env or as a repository secret. Example:
   - `owner:your-username/*`
   - `owner:your-org/*`
3. Review/adjust defaults in `renovate.json` to match your preferences (labels, automerge, dashboards, etc.).

## How it runs

- Scheduled daily at 03:00 UTC.
- Can be run manually: Actions tab -> "Renovate Runner" -> "Run workflow".
- Uses `renovatebot/github-action` with autodiscovery enabled to find all repositories accessible to the token.

## Notes

- The default configuration sets `RENOVATE_ONBOARDING=false` and `RENOVATE_REQUIRE_CONFIG=false` so it can run without having to add a `renovate.json` in every target repo. If you prefer per-repo configs and onboarding PRs, set these to `true`.
- Safety limits are configured via `RENOVATE_PR_HOURLY_LIMIT` and `RENOVATE_PR_CONCURRENT_LIMIT`.
- Add or remove managers in `renovate.json` based on the ecosystems you use.
