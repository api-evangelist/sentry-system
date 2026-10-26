# Sentry (sentry-system)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Sentry is an open-source error tracking and performance monitoring platform that helps developers identify, triage, and resolve issues in their applications in real-time.

**APIs.json:** [https://sentry.io/apis.json](https://sentry.io/apis.json)

## Tags

- APM
- Application Monitoring
- Bug Tracking
- Developer Tools
- Error Tracking
- Observability
- Performance Monitoring
- Real-Time Monitoring

## Timestamps

- **Created:** 2024-01-15
- **Modified:** 2026-05-19

## APIs

### Sentry API

The Sentry API provides programmatic access to Sentry's error tracking, performance monitoring, and organizational data. It allows developers to manage projects, issues, releases, and more.

- **Human URL:** [https://sentry.io](https://sentry.io)
- **Base URL:** `https://sentry.io/api/0`

#### Tags

- Error Tracking
- Monitoring
- Observability
- Performance

#### Properties

- [Documentation](https://docs.sentry.io/api/)
- [OpenAPI](https://github.com/getsentry/sentry-api-schema/blob/main/openapi-derefed.json) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [OpenAPI](openapi/sentry-api-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/sentry-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/sentry-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [Authentication](https://docs.sentry.io/api/auth/)
- [Getting Started](https://docs.sentry.io/api/guides/create-auth-token/)
- [Rate Limits](https://docs.sentry.io/api/ratelimits/)
- [S D Ks](https://docs.sentry.io/platforms/)
- [Postman Collection](https://www.postman.com/sentryio/sentry-api-collection/documentation/xtzhckg/sentry-api-collection) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Webhooks](https://docs.sentry.io/organization/integrations/integration-platform/webhooks/)
- [Pagination](https://docs.sentry.io/api/pagination/)
- [Permissions](https://docs.sentry.io/api/permissions/)
- [Reference](https://docs.sentry.io/api/requests/)
- [JSON Schema](json-schema/sentry-organization-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Schema](json-schema/sentry-project-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Schema](json-schema/sentry-issue-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Schema](json-schema/sentry-event-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Schema](json-schema/sentry-team-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Schema](json-schema/sentry-release-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Schema](json-schema/sentry-alert-rule-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Schema](json-schema/sentry-monitor-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Schema](json-schema/sentry-replay-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON-LD](json-ld/sentry-context.jsonld) — [JSON-LD](https://www.w3.org/TR/json-ld11/)

### Sentry Events and Issues API

The Events and Issues API provides endpoints for managing error events and issues in Sentry, including listing, retrieving, updating, and bulk-mutating issues and their associated events, tags, and hashes.

- **Human URL:** [https://docs.sentry.io/api/events/](https://docs.sentry.io/api/events/)
- **Base URL:** `https://sentry.io/api/0`

#### Tags

- Debugging
- Error Tracking
- Events
- Issues

#### Properties

- [Documentation](https://docs.sentry.io/api/events/)
- [OpenAPI](https://github.com/getsentry/sentry-api-schema/blob/main/openapi-derefed.json) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [OpenAPI](openapi/sentry-events-issues-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/sentry-events-issues.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/sentry-events-issues.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Sentry Organizations API

The Organizations API provides endpoints for managing Sentry organizations, including retrieving and updating organization details, managing members and their roles, listing projects and repositories, and resolving event and short IDs.

- **Human URL:** [https://docs.sentry.io/api/organizations/](https://docs.sentry.io/api/organizations/)
- **Base URL:** `https://sentry.io/api/0`

#### Tags

- Administration
- Members
- Organizations

#### Properties

- [Documentation](https://docs.sentry.io/api/organizations/)
- [OpenAPI](https://github.com/getsentry/sentry-api-schema/blob/main/openapi-derefed.json) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [OpenAPI](openapi/sentry-organizations-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/sentry-organizations.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/sentry-organizations.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Sentry Projects API

The Projects API provides endpoints for managing Sentry projects, including creating, retrieving, updating, and deleting projects, as well as managing client keys, debug files, service hooks, filters, and user feedback.

- **Human URL:** [https://docs.sentry.io/api/projects/](https://docs.sentry.io/api/projects/)
- **Base URL:** `https://sentry.io/api/0`

#### Tags

- Configuration
- Management
- Projects

#### Properties

- [Documentation](https://docs.sentry.io/api/projects/)
- [OpenAPI](https://github.com/getsentry/sentry-api-schema/blob/main/openapi-derefed.json) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [OpenAPI](openapi/sentry-projects-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/sentry-projects.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/sentry-projects.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Sentry Teams API

The Teams API provides endpoints for managing teams within Sentry organizations, including creating, retrieving, updating, and deleting teams, managing team members and their roles, and listing projects associated with teams.

- **Human URL:** [https://docs.sentry.io/api/teams/](https://docs.sentry.io/api/teams/)
- **Base URL:** `https://sentry.io/api/0`

#### Tags

- Collaboration
- Members
- Teams

#### Properties

- [Documentation](https://docs.sentry.io/api/teams/)
- [OpenAPI](https://github.com/getsentry/sentry-api-schema/blob/main/openapi-derefed.json) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [OpenAPI](openapi/sentry-teams-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/sentry-teams.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/sentry-teams.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Sentry Releases API

The Releases API provides endpoints for managing software releases in Sentry, including creating and managing releases, uploading release files, listing commits, managing deployments, and retrieving release health session statistics.

- **Human URL:** [https://docs.sentry.io/api/releases/](https://docs.sentry.io/api/releases/)
- **Base URL:** `https://sentry.io/api/0`

#### Tags

- Deployments
- Releases
- Version Management

#### Properties

- [Documentation](https://docs.sentry.io/api/releases/)
- [OpenAPI](https://github.com/getsentry/sentry-api-schema/blob/main/openapi-derefed.json) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [OpenAPI](openapi/sentry-releases-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/sentry-releases.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/sentry-releases.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Sentry Alerts API

The Alerts API provides endpoints for managing alert rules in Sentry, including creating, retrieving, updating, and deleting metric alert rules and issue alert rules, as well as managing spike protection notification actions.

- **Human URL:** [https://docs.sentry.io/api/alerts/](https://docs.sentry.io/api/alerts/)
- **Base URL:** `https://sentry.io/api/0`

#### Tags

- Alerts
- Monitoring
- Notifications

#### Properties

- [Documentation](https://docs.sentry.io/api/alerts/)
- [OpenAPI](https://github.com/getsentry/sentry-api-schema/blob/main/openapi-derefed.json) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [OpenAPI](openapi/sentry-alerts-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/sentry-alerts.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/sentry-alerts.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Sentry Crons API

The Crons API provides endpoints for managing cron job monitors in Sentry, including creating, retrieving, updating, and deleting monitors, managing monitor environments, and retrieving check-in history for tracked scheduled tasks.

- **Human URL:** [https://docs.sentry.io/api/crons/](https://docs.sentry.io/api/crons/)
- **Base URL:** `https://sentry.io/api/0`

#### Tags

- Crons
- Monitoring
- Scheduled Tasks

#### Properties

- [Documentation](https://docs.sentry.io/api/crons/)
- [OpenAPI](https://github.com/getsentry/sentry-api-schema/blob/main/openapi-derefed.json) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [OpenAPI](openapi/sentry-crons-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/sentry-crons.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/sentry-crons.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Sentry Dashboards API

The Dashboards API provides endpoints for managing custom dashboards in Sentry organizations, including creating, listing, retrieving, editing, and deleting dashboards for visualizing error and performance data.

- **Human URL:** [https://docs.sentry.io/api/dashboards/](https://docs.sentry.io/api/dashboards/)
- **Base URL:** `https://sentry.io/api/0`

#### Tags

- Analytics
- Dashboards
- Visualization

#### Properties

- [Documentation](https://docs.sentry.io/api/dashboards/)
- [OpenAPI](https://github.com/getsentry/sentry-api-schema/blob/main/openapi-derefed.json) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [OpenAPI](openapi/sentry-dashboards-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/sentry-dashboards.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/sentry-dashboards.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Sentry Discover API

The Discover and Performance API provides endpoints for managing saved queries in Sentry, allowing you to create, list, retrieve, edit, and delete organization-level Discover saved queries for slicing and analyzing error and transaction event data.

- **Human URL:** [https://docs.sentry.io/api/discover/](https://docs.sentry.io/api/discover/)
- **Base URL:** `https://sentry.io/api/0`

#### Tags

- Analytics
- Discover
- Performance
- Queries

#### Properties

- [Documentation](https://docs.sentry.io/api/discover/)
- [OpenAPI](https://github.com/getsentry/sentry-api-schema/blob/main/openapi-derefed.json) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [OpenAPI](openapi/sentry-discover-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/sentry-discover.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/sentry-discover.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Sentry Environments API

The Environments API provides endpoints for managing environments in Sentry, including listing project and organization environments, retrieving individual project environment details, and updating project environment settings.

- **Human URL:** [https://docs.sentry.io/api/environments/](https://docs.sentry.io/api/environments/)
- **Base URL:** `https://sentry.io/api/0`

#### Tags

- Configuration
- Environments
- Management

#### Properties

- [Documentation](https://docs.sentry.io/api/environments/)
- [OpenAPI](https://github.com/getsentry/sentry-api-schema/blob/main/openapi-derefed.json) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [OpenAPI](openapi/sentry-environments-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/sentry-environments.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/sentry-environments.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Sentry Explore API

The Explore API provides endpoints for querying and analyzing event data in Sentry, allowing you to slice and dice events in both table and timeseries formats for flexible data analysis and visualization.

- **Human URL:** [https://docs.sentry.io/api/explore/](https://docs.sentry.io/api/explore/)
- **Base URL:** `https://sentry.io/api/0`

#### Tags

- Analytics
- Events
- Explore

#### Properties

- [Documentation](https://docs.sentry.io/api/explore/)
- [OpenAPI](https://github.com/getsentry/sentry-api-schema/blob/main/openapi-derefed.json) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [OpenAPI](openapi/sentry-explore-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/sentry-explore.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/sentry-explore.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Sentry Integration Platform API

The Integration Platform API provides endpoints for managing custom integrations in Sentry, including creating and updating external issues, managing integration configurations, listing installations, and retrieving organization-level custom integration details.

- **Human URL:** [https://docs.sentry.io/api/integration/](https://docs.sentry.io/api/integration/)
- **Base URL:** `https://sentry.io/api/0`

#### Tags

- Integrations
- Platform
- Webhooks

#### Properties

- [Documentation](https://docs.sentry.io/api/integration/)
- [OpenAPI](https://github.com/getsentry/sentry-api-schema/blob/main/openapi-derefed.json) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [OpenAPI](openapi/sentry-integration-platform-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/sentry-integration-platform.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/sentry-integration-platform.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Sentry Integrations API

The Integrations API provides endpoints for managing third-party integrations within Sentry organizations, including creating and managing data forwarders, handling external user and team records, and controlling integration configurations.

- **Human URL:** [https://docs.sentry.io/api/integrations/](https://docs.sentry.io/api/integrations/)
- **Base URL:** `https://sentry.io/api/0`

#### Tags

- Configuration
- Data Forwarding
- Integrations

#### Properties

- [Documentation](https://docs.sentry.io/api/integrations/)
- [OpenAPI](https://github.com/getsentry/sentry-api-schema/blob/main/openapi-derefed.json) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [OpenAPI](openapi/sentry-integrations-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/sentry-integrations.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/sentry-integrations.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Sentry Mobile Builds API

The Mobile Builds API provides endpoints for analyzing mobile application artifacts in Sentry, including retrieving install information and size analysis results for mobile build artifacts.

- **Human URL:** [https://docs.sentry.io/api/mobile-builds/](https://docs.sentry.io/api/mobile-builds/)
- **Base URL:** `https://sentry.io/api/0`

#### Tags

- Artifacts
- Builds
- Mobile

#### Properties

- [Documentation](https://docs.sentry.io/api/mobile-builds/)
- [OpenAPI](https://github.com/getsentry/sentry-api-schema/blob/main/openapi-derefed.json) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [OpenAPI](openapi/sentry-mobile-builds-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/sentry-mobile-builds.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/sentry-mobile-builds.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Sentry Monitors API

The Monitors and Alerts API provides beta endpoints for managing monitors and alerts in Sentry, including creating, retrieving, updating, and deleting monitors and alerts, as well as bulk operations at the organization level.

- **Human URL:** [https://docs.sentry.io/api/monitors/](https://docs.sentry.io/api/monitors/)
- **Base URL:** `https://sentry.io/api/0`

#### Tags

- Alerts
- Monitors
- Uptime

#### Properties

- [Documentation](https://docs.sentry.io/api/monitors/)
- [OpenAPI](https://github.com/getsentry/sentry-api-schema/blob/main/openapi-derefed.json) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [OpenAPI](openapi/sentry-monitors-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/sentry-monitors.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/sentry-monitors.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Sentry Prevent API

The Prevent API provides endpoints for managing repository integrations and test results in Sentry, including syncing repositories, managing branches, generating upload tokens, and retrieving test result metrics and test suites.

- **Human URL:** [https://docs.sentry.io/api/prevent/](https://docs.sentry.io/api/prevent/)
- **Base URL:** `https://sentry.io/api/0`

#### Tags

- Quality Assurance
- Repositories
- Testing

#### Properties

- [Documentation](https://docs.sentry.io/api/prevent/)
- [OpenAPI](https://github.com/getsentry/sentry-api-schema/blob/main/openapi-derefed.json) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [OpenAPI](openapi/sentry-prevent-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/sentry-prevent.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/sentry-prevent.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Sentry Replays API

The Replays API provides endpoints for managing session replays in Sentry, including listing, retrieving, and deleting replay instances, accessing recording segments, listing clicked nodes and selectors, and retrieving replay counts for issues or transactions.

- **Human URL:** [https://docs.sentry.io/api/replays/](https://docs.sentry.io/api/replays/)
- **Base URL:** `https://sentry.io/api/0`

#### Tags

- Replays
- Session Recording
- User Experience

#### Properties

- [Documentation](https://docs.sentry.io/api/replays/)
- [OpenAPI](https://github.com/getsentry/sentry-api-schema/blob/main/openapi-derefed.json) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [OpenAPI](openapi/sentry-replays-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/sentry-replays.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/sentry-replays.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Sentry SCIM API

The SCIM API provides endpoints for federated identity management in Sentry, enabling automated provisioning and deprovisioning of organization members and teams using the System for Cross-Domain Identity Management standard. Requires a Business Plan with SAML2 enabled.

- **Human URL:** [https://docs.sentry.io/api/scim/](https://docs.sentry.io/api/scim/)
- **Base URL:** `https://sentry.io/api/0`

#### Tags

- Enterprise
- Identity Management
- Provisioning
- SCIM

#### Properties

- [Documentation](https://docs.sentry.io/api/scim/)
- [OpenAPI](https://github.com/getsentry/sentry-api-schema/blob/main/openapi-derefed.json) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [OpenAPI](openapi/sentry-scim-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/sentry-scim.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/sentry-scim.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Sentry Seer API

The Seer API provides endpoints for AI-powered issue analysis in Sentry, including listing available AI models, starting AI-assisted issue fixes, and retrieving the state of ongoing fix analyses.

- **Human URL:** [https://docs.sentry.io/api/seer/](https://docs.sentry.io/api/seer/)
- **Base URL:** `https://sentry.io/api/0`

#### Tags

- AI
- Automation
- Issue Analysis

#### Properties

- [Documentation](https://docs.sentry.io/api/seer/)
- [OpenAPI](https://github.com/getsentry/sentry-api-schema/blob/main/openapi-derefed.json) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [OpenAPI](openapi/sentry-seer-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/sentry-seer.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/sentry-seer.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Sentry Users API

The Users API provides endpoints for retrieving user-related information in Sentry, including listing the organizations that the authenticated user belongs to.

- **Human URL:** [https://docs.sentry.io/api/users/](https://docs.sentry.io/api/users/)
- **Base URL:** `https://sentry.io/api/0`

#### Tags

- Accounts
- Authentication
- Users

#### Properties

- [Documentation](https://docs.sentry.io/api/users/)
- [OpenAPI](https://github.com/getsentry/sentry-api-schema/blob/main/openapi-derefed.json) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [OpenAPI](openapi/sentry-users-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/sentry-users.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/sentry-users.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

## Common Properties

- [Arazzo Workflows](arazzo/) — [Arazzo Specification](https://spec.openapis.org/arazzo/latest.html)
- [Portal](https://sentry.io/)
- [Website](https://sentry.io/)
- [Documentation](https://docs.sentry.io/)
- [Getting Started](https://docs.sentry.io/product/sentry-basics/)
- [Authentication](https://docs.sentry.io/api/auth/)
- [Pricing](https://sentry.io/pricing/)
- [Blog](https://blog.sentry.io/)
- [GitHub Organization](https://github.com/getsentry)
- [Discord](https://discord.gg/sentry)
- [Sign Up](https://sentry.io/signup/)
- [Login](https://sentry.io/auth/login/)
- [Support](https://sentry.zendesk.com/hc/en-us/)
- [Forum](https://forum.sentry.io/)
- [Changelog](https://sentry.io/changelog/)
- [Security](https://sentry.io/security/)
- [Community](https://sentry.io/community/)
- [Self- Hosted](https://docs.sentry.io/server/)
- [Developer  Documentation](https://develop.sentry.dev/)
- [C L I](https://docs.sentry.io/cli/)
- [S D Ks](https://docs.sentry.io/platforms/)
- [Status Page](https://status.sentry.io/)
- [Terms of Service](https://sentry.io/terms/)
- [Privacy Policy](https://sentry.io/privacy/)
- [Integrations](https://sentry.io/integrations/)
- [L L Ms Txt](https://docs.sentry.io/llms.txt)

## Maintainers

**Email:** kin@apievangelist.com
