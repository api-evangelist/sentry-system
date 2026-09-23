---
name: sentry-provision-project-and-key
description: Create a Sentry project under a team and mint the client key (DSN) an application needs to start reporting.
api: Sentry Web API (https://sentry.io/api/0)
generated: '2026-09-18'
method: generated
source: arazzo/sentry-system-provision-project-key-workflow.yml + openapi/sentry-system-projects-api-openapi.yml + openapi/sentry-system-client-keys-api-openapi.yml
operations:
  - createProject
  - retrieveProject
  - createProjectClientKey
  - listProjectClientKeys
---

# Provision a Sentry project and client key

The onboarding path: a new service needs somewhere to send events and a DSN to send them with.

## Steps

1. **Create the project** — `createProject` → `POST /teams/{organization_id_or_slug}/{team_id_or_slug}/projects/`.
   A project is always created *under a team*; there is no org-level create. Supply `name`, and `slug`
   if you want to control the addressable identifier.
2. **Confirm it** — `retrieveProject` → `GET /projects/{organization_id_or_slug}/{project_id_or_slug}/`.
   Keep the returned `slug`: nearly every other project endpoint addresses projects by slug, not id.
3. **Mint a client key** — `createProjectClientKey` → `POST /projects/{organization_id_or_slug}/{project_id_or_slug}/keys/`.
   The response carries the DSN under `dsn.public` — that is the value an SDK is configured with.
4. **Verify** — `listProjectClientKeys` → `GET /projects/{organization_id_or_slug}/{project_id_or_slug}/keys/`.

## Rules an agent must follow

- **`deleteProject` is declared irreversible by Sentry's own contract** ("Schedules a project for
  deletion. This action is irreversible."). Never use it to clean up a mistaken create without an
  explicit human instruction naming the project.
- **The DSN is a credential.** Treat `dsn.public` as a secret in logs and transcripts even though it is
  client-side by design.
- **There is no idempotency key on these POSTs.** A retried `createProject` after a network timeout can
  produce a second project. Before retrying, call `retrieveProject` on the slug you intended and only
  re-POST if it 404s.
- Required scopes: `project:write` (create project, create key), `project:read` (read back).
