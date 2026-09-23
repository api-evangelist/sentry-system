---
name: sentry-run-seer-autofix
description: Start Sentry's Seer AI root-cause and fix run on an issue and poll its state to completion.
api: Sentry Web API (https://sentry.io/api/0)
generated: '2026-09-18'
method: generated
source: arazzo/sentry-system-seer-autofix-issue-workflow.yml + openapi/sentry-system-seer-api-openapi.yml
operations:
  - listSeerModels
  - startSeerIssueFix
  - retrieveSeerIssueFixState
---

# Run a Seer autofix on an issue

Seer is Sentry's AI agent: it takes an issue, works out a root cause, proposes a solution, and can open
a pull request. An agent driving Seer is an agent driving another agent — be deliberate about it.

## Steps

1. **Check available models** — `listSeerModels` →
   `GET /organizations/{organization_id_or_slug}/seer/models/`.
2. **Start the run** — `startSeerIssueFix` → `POST /issues/{issue_id}/seer/autofix/`. The response
   carries a `run_id`.
3. **Poll state** — `retrieveSeerIssueFixState` → `GET /issues/{issue_id}/seer/autofix/` until the run
   reaches a terminal state.

## Rules an agent must follow

- **Prefer the event feed over polling.** The `seer` webhook resource emits
  `seer.root_cause_started`, `seer.root_cause_completed`, `seer.solution_started`,
  `seer.solution_completed`, `seer.coding_started`, `seer.coding_completed` and `seer.pr_created`,
  each carrying `run_id` and `group_id`. Subscribing beats a poll loop that will hit the per-endpoint
  rate limit. See `asyncapi/sentry-system-webhooks.yml`.
- **Seer can open a pull request.** `seer.pr_created` carries `data.details.pull_requests[]` with
  `repo_name` and `url`. That is a write into a code host, outside Sentry, and outside anything this
  API can reverse. Do not start a coding run on a repository the user has not named.
- **The run is not idempotent.** A retried `startSeerIssueFix` after a timeout may start a second run;
  call `retrieveSeerIssueFixState` first.
- Seer availability depends on the organization's plan and on consent settings; a 403 here is a
  product-entitlement answer, not an auth bug.
