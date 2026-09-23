---
name: sentry-create-cron-monitor
description: Create a Sentry cron monitor for a scheduled job and confirm its check-ins are landing.
api: Sentry Web API (https://sentry.io/api/0)
generated: '2026-09-18'
method: generated
source: arazzo/sentry-system-create-cron-monitor-workflow.yml + openapi/sentry-system-monitors-api-openapi.yml + openapi/sentry-system-check-ins-api-openapi.yml
operations:
  - createMonitor
  - retrieveMonitor
  - listMonitorCheckIns
---

# Create a cron monitor and verify check-ins

Use this when a scheduled job needs missed-run and failure alerting.

## Steps

1. **Create the monitor** — `createMonitor` → `POST /organizations/{organization_id_or_slug}/monitors/`.
   The monitor config carries the schedule (`crontab` or `interval`), `checkin_margin`,
   `max_runtime` and `timezone`. Set the schedule to exactly what the job's scheduler runs, or you
   will alert on a phantom miss.
2. **Confirm** — `retrieveMonitor` →
   `GET /organizations/{organization_id_or_slug}/monitors/{monitor_id_or_slug}/`.
3. **Verify check-ins arrive** — `listMonitorCheckIns` →
   `GET /organizations/{organization_id_or_slug}/monitors/{monitor_id_or_slug}/checkins/`. An empty
   list after a scheduled window has passed means the job is not checking in, not that the monitor is
   broken.

## Rules an agent must follow

- **Do not create a monitor without knowing the job's real schedule and timezone.** A wrong `timezone`
  produces false "missed" incidents that page a human.
- **Reversal:** `deleteMonitor` exists (and can selectively delete monitor environments via the
  `environment` query parameter). No restore window is documented — treat deletion as one-way.
- Wire alerting through `asyncapi/sentry-system-webhooks.yml` rather than polling `listMonitorCheckIns`
  on a tight loop; polling is the documented fast path to a 429.
- Required scopes: `project:write` to create, `project:read` to read back.
