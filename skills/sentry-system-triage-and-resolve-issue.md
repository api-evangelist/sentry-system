---
name: sentry-triage-and-resolve-issue
description: Find a high-priority unresolved Sentry issue, read its detail and recent events, then assign an owner and resolve it.
api: Sentry Web API (https://sentry.io/api/0)
generated: '2026-09-18'
method: generated
source: arazzo/sentry-system-triage-resolve-issue-workflow.yml + openapi/sentry-system-issues-api-openapi.yml + openapi/sentry-system-events-api-openapi.yml
operations:
  - listOrganizationIssues
  - retrieveIssue
  - listIssueEvents
  - updateIssue
---

# Triage and resolve a Sentry issue

Use this when an agent has to work an incident queue: pick the issue that matters, gather enough
context to say what broke, then change the issue's state.

## Before you start

- Auth: `Authorization: Bearer <token>`. Any of a user auth token, an organization auth token, an
  internal-integration token, or an OAuth token works. See `authentication/sentry-system-authentication.yml`.
- Required scopes: `event:read` to read, `event:write` to change issue state. See `scopes/sentry-system-scopes.yml`.
- Base URL: `https://sentry.io/api/0`.

## Steps

1. **List candidates** — `listOrganizationIssues` → `GET /organizations/{organization_id_or_slug}/issues/`.
   Narrow with the `query` parameter using Sentry search syntax, e.g.
   `is:unresolved issue.priority:[high,medium]`. The response is a bare JSON array; the next page is in
   the `Link` header (`rel="next"`, `results="true"`), not in the body.
2. **Read the issue** — `retrieveIssue` → `GET /issues/{issue_id}/`. Read `status`, `substatus` and
   `statusDetails` before deciding anything: `unresolved` can mean `new`, `ongoing`, `escalating` or
   `regressed`, and those call for different actions.
3. **Read recent events** — `listIssueEvents` → `GET /issues/{issue_id}/events/`. Use the latest event's
   stack trace, `culprit` and `contexts` for the root-cause summary. Do not summarize from the issue
   title alone.
4. **Act** — `updateIssue` → `PUT /issues/{issue_id}/`. Send only the attributes you are changing;
   the endpoint modifies only what is submitted. Typical body: `{"status": "resolved", "assignedTo": "<user or team>"}`.

## Rules an agent must follow

- **This write is reversible, and that is the safety net you have.** `updateIssue` can move the issue
  back with `{"status": "unresolved"}`; no window is documented, so the reversal is not time-boxed.
  `removeIssue` (`DELETE /issues/{issue_id}/`) is NOT reversible — never use it as an undo.
- **No idempotency key exists.** Sentry documents no `Idempotency-Key` header. `updateIssue` is a PUT
  and is naturally idempotent for the same body, so a retry after a timeout is safe; do not assume the
  same for any POST.
- **Rate limits are per caller + per endpoint.** Read `X-Sentry-Rate-Limit-Remaining` and
  `X-Sentry-Rate-Limit-ConcurrentRemaining` on every response and back off on 429 using
  `X-Sentry-Rate-Limit-Reset`. Generating more tokens does not raise the limit — the limiter keys on
  identity, not token.
- **Do not poll for new issues.** Sentry explicitly recommends webhooks over polling; the `issue`
  resource webhook fires on `created`, `resolved`, `assigned`, `archived` and `unresolved`. See
  `asyncapi/sentry-system-webhooks.yml`.
- Errors come back as `{"detail": "..."}` (400 validation adds per-field arrays), not RFC 9457. See
  `errors/sentry-system-problem-types.yml`.
