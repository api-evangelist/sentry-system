---
name: sentry-cut-release-and-record-deploy
description: Register a release in Sentry and record a deploy against it, so regressions and resolutions can be tied to a version.
api: Sentry Web API (https://sentry.io/api/0)
generated: '2026-09-18'
method: generated
source: arazzo/sentry-system-cut-release-deploy-workflow.yml + openapi/sentry-system-releases-api-openapi.yml + openapi/sentry-system-deploys-api-openapi.yml
operations:
  - createOrganizationRelease
  - retrieveOrganizationRelease
  - createReleaseDeploy
  - listReleaseDeploys
---

# Cut a release and record a deploy

This is the CI-side flow. Run it from a pipeline after a build is produced and again after it ships.

## Steps

1. **Create the release** — `createOrganizationRelease` →
   `POST /organizations/{organization_id_or_slug}/releases/`. `version` is the release identifier and
   must match what the SDK reports at runtime (`release` in SDK init) or nothing will correlate.
   Attach `projects` and, where available, `refs`/`commits` so Sentry can do commit-level suspect
   attribution.
2. **Confirm** — `retrieveOrganizationRelease` →
   `GET /organizations/{organization_id_or_slug}/releases/{version}/`.
3. **Record the deploy** — `createReleaseDeploy` →
   `POST /organizations/{organization_id_or_slug}/releases/{version}/deploys/`. Supply `environment`
   (e.g. `production`) and, if known, `dateStarted`/`dateFinished`.
4. **Verify** — `listReleaseDeploys` →
   `GET /organizations/{organization_id_or_slug}/releases/{version}/deploys/`.

## Rules an agent must follow

- **Version strings are the join key.** Do not invent or normalize them. Use the exact string the build
  produced and the SDK is configured with.
- **Reversal:** `deleteOrganizationRelease` (`DELETE /organizations/{organization_id_or_slug}/releases/{version}/`)
  exists and removes a release, but Sentry documents no restore window or undo — treat it as one-way.
  There is no delete for a deploy.
- **Re-running step 1 for an existing version is not an error path to rely on.** Read the release back
  with step 2 first; the API has no idempotency-key mechanism.
- Required scopes: `project:releases` for both release and deploy writes.
