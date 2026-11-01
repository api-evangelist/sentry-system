# Sentry

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

Sentry is an open-source error tracking and performance monitoring platform that helps developers identify, triage, and resolve issues in their applications in real-time. Its Web API at https://sentry.io/api/0 covers issues and events, releases and deploys, alerting, dashboards and Discover queries, session replays, cron and uptime monitors, mobile build size analysis, SCIM 2.0 provisioning, and the Seer AI agent that root-causes an issue and can open a pull request. Sentry also runs a first-party remote MCP server at https://mcp.sentry.dev/mcp and ships official SDKs for 20+ platforms.

**Areas:** APM · Application Monitoring · Bug Tracking · Developer Tools · Error Tracking · Observability · Performance Monitoring · Real-Time Monitoring · Monitoring

## Sentry on APIs.io

Everything profiled for Sentry is published on APIs.io rather than in this repository. Start at the provider page — **[apis.io/providers/sentry-system/](https://apis.io/providers/sentry-system/)** — or go straight to one of the APIs or properties below.

### APIs (45)

- [Sentry Alerts API](https://apis.io/apis/sentry-system/sentry-system-alerts-api/) — Manage alerts (beta)
- [Sentry Check-Ins API](https://apis.io/apis/sentry-system/sentry-system-check-ins-api/) — View monitor check-in history
- [Sentry Client Keys API](https://apis.io/apis/sentry-system/sentry-system-client-keys-api/) — Manage project client keys (DSNs)
- [Sentry Commits API](https://apis.io/apis/sentry-system/sentry-system-commits-api/) — Manage commits associated with releases
- [Sentry Custom Integrations API](https://apis.io/apis/sentry-system/sentry-system-custom-integrations-api/) — Manage custom integrations
- [Sentry Dashboards API](https://apis.io/apis/sentry-system/sentry-system-dashboards-api/) — Manage custom dashboards and their widgets
- [Sentry Data Forwarders API](https://apis.io/apis/sentry-system/sentry-system-data-forwarders-api/) — Manage data forwarding configurations
- [Sentry Debug Files API](https://apis.io/apis/sentry-system/sentry-system-debug-files-api/) — Manage debug information files
- [Sentry Deploys API](https://apis.io/apis/sentry-system/sentry-system-deploys-api/) — Manage deployments of releases
- [Sentry Discover API](https://apis.io/apis/sentry-system/sentry-system-discover-api/) — Manage saved Discover queries
- [Sentry Environments API](https://apis.io/apis/sentry-system/sentry-system-environments-api/) — Manage project and organization environments
- [Sentry Events API](https://apis.io/apis/sentry-system/sentry-system-events-api/) — Access and manage error and transaction events
- [Sentry Explore API](https://apis.io/apis/sentry-system/sentry-system-explore-api/) — Query and analyze event data
- [Sentry External Issues API](https://apis.io/apis/sentry-system/sentry-system-external-issues-api/) — Manage external issue links
- [Sentry External Teams API](https://apis.io/apis/sentry-system/sentry-system-external-teams-api/) — Manage external team mappings
- [Sentry External Users API](https://apis.io/apis/sentry-system/sentry-system-external-users-api/) — Manage external user mappings
- [Sentry Filters API](https://apis.io/apis/sentry-system/sentry-system-filters-api/) — Manage inbound data filters
- [Sentry Installations API](https://apis.io/apis/sentry-system/sentry-system-installations-api/) — Manage integration installations
- [Sentry Integrations API](https://apis.io/apis/sentry-system/sentry-system-integrations-api/) — Manage third-party integrations
- [Sentry Interactions API](https://apis.io/apis/sentry-system/sentry-system-interactions-api/) — View user interactions within replays
- [Sentry Issue Alerts API](https://apis.io/apis/sentry-system/sentry-system-issue-alerts-api/) — Manage issue alert rules at the project level
- [Sentry Issues API](https://apis.io/apis/sentry-system/sentry-system-issues-api/) — Manage error issues
- [Sentry Members API](https://apis.io/apis/sentry-system/sentry-system-members-api/) — Manage organization members and their roles
- [Sentry Metric Alerts API](https://apis.io/apis/sentry-system/sentry-system-metric-alerts-api/) — Manage metric alert rules at the organization level
- [Sentry Mobile Builds API](https://apis.io/apis/sentry-system/sentry-system-mobile-builds-api/) — Analyze mobile application build artifacts
- [Sentry Monitors API](https://apis.io/apis/sentry-system/sentry-system-monitors-api/) — Manage cron job monitors
- [Sentry Organizations API](https://apis.io/apis/sentry-system/sentry-system-organizations-api/) — Manage Sentry organizations
- [Sentry Projects API](https://apis.io/apis/sentry-system/sentry-system-projects-api/) — Manage projects within organizations
- [Sentry Recording Segments API](https://apis.io/apis/sentry-system/sentry-system-recording-segments-api/) — Access replay recording segments
- [Sentry Release Files API](https://apis.io/apis/sentry-system/sentry-system-release-files-api/) — Manage files associated with releases
- [Sentry Releases API](https://apis.io/apis/sentry-system/sentry-system-releases-api/) — Manage software releases
- [Sentry Replays API](https://apis.io/apis/sentry-system/sentry-system-replays-api/) — Manage session replays
- [Sentry Repositories API](https://apis.io/apis/sentry-system/sentry-system-repositories-api/) — Manage organization repositories
- [Sentry SCIM Members API](https://apis.io/apis/sentry-system/sentry-system-scim-members-api/) — Provision and manage organization members via SCIM
- [Sentry SCIM Teams API](https://apis.io/apis/sentry-system/sentry-system-scim-teams-api/) — Provision and manage teams via SCIM
- [Sentry Seer API](https://apis.io/apis/sentry-system/sentry-system-seer-api/) — AI-powered issue analysis and fix suggestions
- [Sentry Service Hooks API](https://apis.io/apis/sentry-system/sentry-system-service-hooks-api/) — Manage project service hooks
- [Sentry Sessions API](https://apis.io/apis/sentry-system/sentry-system-sessions-api/) — Retrieve release health session statistics
- [Sentry Spike Protection API](https://apis.io/apis/sentry-system/sentry-system-spike-protection-api/) — Manage spike protection notification actions
- [Sentry Teams API](https://apis.io/apis/sentry-system/sentry-system-teams-api/) — Manage teams within organizations
- [Sentry Test Results API](https://apis.io/apis/sentry-system/sentry-system-test-results-api/) — Retrieve test result metrics and test suites
- [Sentry Tokens API](https://apis.io/apis/sentry-system/sentry-system-tokens-api/) — Manage repository upload tokens
- [Sentry User Feedback API](https://apis.io/apis/sentry-system/sentry-system-user-feedback-api/) — Manage user feedback submissions
- [Sentry SDK API](https://apis.io/apis/sentry-system/sentry-sdk-api/) — Sentry provides official SDKs for 22+ platforms including JavaScript, Python, PHP, .NET, Java, Go, Ruby, Rust, Android, Apple, React Native, Unity, and…
- [Sentry Integration Platform API](https://apis.io/apis/sentry-system/sentry-integration-platform-api/) — The Sentry Integration Platform API enables building public and internal integrations with Sentry. Supports OAuth2 with PKCE, device authorization flow for…

### Published properties

Each property below is a page on APIs.io carrying the machine-readable artifact and what it says.

#### Arazzo workflows (12)

Multi-step workflows that chain operations into a business task.

- [Sentry Capture User Feedback for an Event](https://apis.io/arazzos/sentry-system/sentry-system-capture-user-feedback-workflow/)
- [Sentry Create a Cron Monitor and Verify Check-ins](https://apis.io/arazzos/sentry-system/sentry-system-create-cron-monitor-workflow/)
- [Sentry Create a Metric Alert Rule](https://apis.io/arazzos/sentry-system/sentry-system-create-metric-alert-workflow/)
- [Sentry Create a Team and Add a Member](https://apis.io/arazzos/sentry-system/sentry-system-create-team-add-member-workflow/)
- [Sentry Cut a Release and Record a Deploy](https://apis.io/arazzos/sentry-system/sentry-system-cut-release-deploy-workflow/)
- [Sentry Grant a Team Access to a Project](https://apis.io/arazzos/sentry-system/sentry-system-grant-team-project-access-workflow/)
- [Sentry Investigate an Issue's Tags](https://apis.io/arazzos/sentry-system/sentry-system-investigate-issue-tags-workflow/)
- [Sentry Invite a Member and Onboard to a Team](https://apis.io/arazzos/sentry-system/sentry-system-invite-member-onboard-team-workflow/)
- [Sentry Provision a Project and Client Key](https://apis.io/arazzos/sentry-system/sentry-system-provision-project-key-workflow/)
- [Sentry Run a Seer AI Autofix on an Issue](https://apis.io/arazzos/sentry-system/sentry-system-seer-autofix-issue-workflow/)
- [Sentry Sync Prevent Repositories](https://apis.io/arazzos/sentry-system/sentry-system-sync-prevent-repos-workflow/)
- [Sentry Triage and Resolve an Issue](https://apis.io/arazzos/sentry-system/sentry-system-triage-resolve-issue-workflow/)

#### AsyncAPI (1)

Event-driven and webhook contracts.

- [Sentry System Webhooks](https://apis.io/asyncapis/sentry-system/sentry-system-webhooks/)

#### MCP servers (1)

Model Context Protocol servers that expose the API to agents.

- [sentry](https://apis.io/servers/sentry-system/sentry/)

#### Agentic access (1)

Which operations an agent can call, which act, and which need a human in the loop.

- [Sentry System Agentic Access](https://apis.io/agentic-access/sentry-system/sentry-system-agentic-access/)

#### JSON Schema (54)

The data models the API reads and writes.

- [Sentry Alert Rule](https://apis.io/schemas/sentry-system/sentry-alert-rule/)
- [Sentry Event](https://apis.io/schemas/sentry-system/sentry-event/)
- [Sentry Issue](https://apis.io/schemas/sentry-system/sentry-issue/)
- [Sentry Monitor](https://apis.io/schemas/sentry-system/sentry-monitor/)
- [Sentry Organization](https://apis.io/schemas/sentry-system/sentry-organization/)
- [Sentry Project](https://apis.io/schemas/sentry-system/sentry-project/)
- [Sentry Release](https://apis.io/schemas/sentry-system/sentry-release/)
- [Sentry Replay](https://apis.io/schemas/sentry-system/sentry-replay/)
- [CheckIn](https://apis.io/schemas/sentry-system/sentry-system-checkin/)
- [ClientKey](https://apis.io/schemas/sentry-system/sentry-system-clientkey/)
- [Commit](https://apis.io/schemas/sentry-system/sentry-system-commit/)
- [Dashboard](https://apis.io/schemas/sentry-system/sentry-system-dashboard/)
- [DashboardSummary](https://apis.io/schemas/sentry-system/sentry-system-dashboardsummary/)
- [DataForwarder](https://apis.io/schemas/sentry-system/sentry-system-dataforwarder/)
- [DebugFile](https://apis.io/schemas/sentry-system/sentry-system-debugfile/)
- [Deploy](https://apis.io/schemas/sentry-system/sentry-system-deploy/)
- [Environment](https://apis.io/schemas/sentry-system/sentry-system-environment/)
- [Event](https://apis.io/schemas/sentry-system/sentry-system-event/)
- [ExternalIssue](https://apis.io/schemas/sentry-system/sentry-system-externalissue/)
- [Installation](https://apis.io/schemas/sentry-system/sentry-system-installation/)
- [Integration](https://apis.io/schemas/sentry-system/sentry-system-integration/)
- [Issue](https://apis.io/schemas/sentry-system/sentry-system-issue/)
- [IssueAlertRule](https://apis.io/schemas/sentry-system/sentry-system-issuealertrule/)
- [MetricAlertRule](https://apis.io/schemas/sentry-system/sentry-system-metricalertrule/)
- [Monitor](https://apis.io/schemas/sentry-system/sentry-system-monitor/)
- …and 29 more published on APIs.io

#### JSON Structure (1)

Data models expressed in JSON Structure.

- [Sentry System Structure](https://apis.io/json-structure/sentry-system/sentry-system-structure/)

#### JSON-LD (1)

Linked-data contexts for the provider's vocabulary.

- [Sentry Context](https://apis.io/jsonld/sentry-system/sentry-context/)

#### Vocabulary (1)

The provider's domain terms, defined.

- [Sentry Domain Vocabulary](https://apis.io/vocabularies/sentry-system/sentry-system-vocabulary/)

#### Collections (86)

Postman and Open Collection request collections.

- [Sentry Alerts API](https://apis.io/collections/sentry-system/open-sentry-alerts/)
- [Sentry API](https://apis.io/collections/sentry-system/open-sentry-api/)
- [Sentry Crons API](https://apis.io/collections/sentry-system/open-sentry-crons/)
- [Sentry Dashboards API](https://apis.io/collections/sentry-system/open-sentry-dashboards/)
- [Sentry Discover API](https://apis.io/collections/sentry-system/open-sentry-discover/)
- [Sentry Environments API](https://apis.io/collections/sentry-system/open-sentry-environments/)
- [Sentry Events and Issues API](https://apis.io/collections/sentry-system/open-sentry-events-issues/)
- [Sentry Explore API](https://apis.io/collections/sentry-system/open-sentry-explore/)
- [Sentry Integration Platform API](https://apis.io/collections/sentry-system/open-sentry-integration-platform/)
- [Sentry Integrations API](https://apis.io/collections/sentry-system/open-sentry-integrations/)
- [Sentry Mobile Builds API](https://apis.io/collections/sentry-system/open-sentry-mobile-builds/)
- [Sentry Monitors API](https://apis.io/collections/sentry-system/open-sentry-monitors/)
- [Sentry Organizations API](https://apis.io/collections/sentry-system/open-sentry-organizations/)
- [Sentry Prevent API](https://apis.io/collections/sentry-system/open-sentry-prevent/)
- [Sentry Projects API](https://apis.io/collections/sentry-system/open-sentry-projects/)
- [Sentry Releases API](https://apis.io/collections/sentry-system/open-sentry-releases/)
- [Sentry Replays API](https://apis.io/collections/sentry-system/open-sentry-replays/)
- [Sentry SCIM API](https://apis.io/collections/sentry-system/open-sentry-scim/)
- [Sentry Seer API](https://apis.io/collections/sentry-system/open-sentry-seer/)
- [Sentry Alerts API](https://apis.io/collections/sentry-system/open-sentry-system-alerts-api/)
- [Sentry Alerts Check-Ins API](https://apis.io/collections/sentry-system/open-sentry-system-check-ins-api/)
- [Sentry Alerts Client Keys API](https://apis.io/collections/sentry-system/open-sentry-system-client-keys-api/)
- [Sentry Alerts Commits API](https://apis.io/collections/sentry-system/open-sentry-system-commits-api/)
- [Sentry Alerts Custom Integrations API](https://apis.io/collections/sentry-system/open-sentry-system-custom-integrations-api/)
- [Sentry Alerts Dashboards API](https://apis.io/collections/sentry-system/open-sentry-system-dashboards-api/)
- …and 61 more published on APIs.io

#### Overlays (64)

OpenAPI overlays, including conversational phrasing for each operation.

- [Sentry System Alerts Api Phrasing Overlay](https://apis.io/overlays/sentry-system/sentry-system-alerts-api-phrasing-overlay/)
- [Sentry System Alerts Overlay](https://apis.io/overlays/sentry-system/sentry-system-alerts-overlay/)
- [Sentry System Api Overlay](https://apis.io/overlays/sentry-system/sentry-system-api-overlay/)
- [Sentry System Check Ins Api Phrasing Overlay](https://apis.io/overlays/sentry-system/sentry-system-check-ins-api-phrasing-overlay/)
- [Sentry System Client Keys Api Phrasing Overlay](https://apis.io/overlays/sentry-system/sentry-system-client-keys-api-phrasing-overlay/)
- [Sentry System Commits Api Phrasing Overlay](https://apis.io/overlays/sentry-system/sentry-system-commits-api-phrasing-overlay/)
- [Sentry System Crons Overlay](https://apis.io/overlays/sentry-system/sentry-system-crons-overlay/)
- [Sentry System Custom Integrations Api Phrasing Overlay](https://apis.io/overlays/sentry-system/sentry-system-custom-integrations-api-phrasing-overlay/)
- [Sentry System Dashboards Api Phrasing Overlay](https://apis.io/overlays/sentry-system/sentry-system-dashboards-api-phrasing-overlay/)
- [Sentry System Dashboards Overlay](https://apis.io/overlays/sentry-system/sentry-system-dashboards-overlay/)
- [Sentry System Data Forwarders Api Phrasing Overlay](https://apis.io/overlays/sentry-system/sentry-system-data-forwarders-api-phrasing-overlay/)
- [Sentry System Debug Files Api Phrasing Overlay](https://apis.io/overlays/sentry-system/sentry-system-debug-files-api-phrasing-overlay/)
- [Sentry System Deploys Api Phrasing Overlay](https://apis.io/overlays/sentry-system/sentry-system-deploys-api-phrasing-overlay/)
- [Sentry System Discover Api Phrasing Overlay](https://apis.io/overlays/sentry-system/sentry-system-discover-api-phrasing-overlay/)
- [Sentry System Discover Overlay](https://apis.io/overlays/sentry-system/sentry-system-discover-overlay/)
- [Sentry System Environments Api Phrasing Overlay](https://apis.io/overlays/sentry-system/sentry-system-environments-api-phrasing-overlay/)
- [Sentry System Environments Overlay](https://apis.io/overlays/sentry-system/sentry-system-environments-overlay/)
- [Sentry System Events Api Phrasing Overlay](https://apis.io/overlays/sentry-system/sentry-system-events-api-phrasing-overlay/)
- [Sentry System Events Issues Overlay](https://apis.io/overlays/sentry-system/sentry-system-events-issues-overlay/)
- [Sentry System Explore Api Phrasing Overlay](https://apis.io/overlays/sentry-system/sentry-system-explore-api-phrasing-overlay/)
- [Sentry System Explore Overlay](https://apis.io/overlays/sentry-system/sentry-system-explore-overlay/)
- [Sentry System External Issues Api Phrasing Overlay](https://apis.io/overlays/sentry-system/sentry-system-external-issues-api-phrasing-overlay/)
- [Sentry System External Teams Api Phrasing Overlay](https://apis.io/overlays/sentry-system/sentry-system-external-teams-api-phrasing-overlay/)
- [Sentry System External Users Api Phrasing Overlay](https://apis.io/overlays/sentry-system/sentry-system-external-users-api-phrasing-overlay/)
- [Sentry System Filters Api Phrasing Overlay](https://apis.io/overlays/sentry-system/sentry-system-filters-api-phrasing-overlay/)
- …and 39 more published on APIs.io

#### Governance rules (1)

Spectral rulesets for the provider's contracts.

- [Sentry — jsonschema rules](https://apis.io/rules/sentry-system/sentry-system-jsonschema-spectral-rules/)

#### Security (4)

Authentication, trust centre, and vulnerability disclosure.

- [Sentry System Authentication](https://apis.io/security/sentry-system/sentry-system-authentication/)
- [Sentry System Domain Security](https://apis.io/security/sentry-system/sentry-system-domain-security/)
- [Sentry System Trust Center](https://apis.io/security/sentry-system/sentry-system-trust-center/)
- [Sentry System Vulnerability Disclosure](https://apis.io/security/sentry-system/sentry-system-vulnerability-disclosure/)

#### OAuth scopes (1)

The permissions the API asks for.

- [Sentry System Scopes](https://apis.io/scopes/sentry-system/sentry-system-scopes/)

#### Plans and pricing (2)

Published plans, tiers and prices.

- [Sentry System Plans Pricing](https://apis.io/plans/sentry-system/sentry-system-plans-pricing/)
- [Sentry System Price Estimates](https://apis.io/plans/sentry-system/sentry-system-price-estimates/)

#### Rate limits (1)

Published request limits.

- [Sentry System Rate Limits](https://apis.io/rate-limits/sentry-system/sentry-system-rate-limits/)

#### FinOps (1)

What the API costs to run, by operation.

- [Sentry System Finops](https://apis.io/finops/sentry-system/sentry-system-finops/)

## Where to find this API

Links to the company's own pages. This repository publishes no copy of their specifications — the canonical document is theirs and lives at the URLs below.

- [Website](https://sentry.io/)
- [Documentation](https://docs.sentry.io/)
- [Getting started](https://docs.sentry.io/product/sentry-basics/)
- [Pricing](https://sentry.io/pricing/)
- [Support](https://sentry.zendesk.com/hc/en-us/)
- [Changelog](https://sentry.io/changelog/)
- [Blog](https://blog.sentry.io/)
- [Security](https://sentry.io/security/)
- [Compliance](https://sentry.io/security/)
- [Terms of service](https://sentry.io/terms/)
- [Privacy policy](https://sentry.io/privacy/)
- [GitHub](https://github.com/getsentry)
- [Status](https://status.sentry.io/)

## API Evangelist rating

**Kin Score 79.0** — exemplar · rubric 0.25.0 · scored 2026-10-23

| Facet | Score |
|---|---|
| Discoverability | 96.7 |
| Contract quality | 70.2 |
| Contract governance | 28.0 |
| Operational transparency | 52.6 |
| Developer ergonomics | 92.3 |
| Access clarity | 85.5 |

**Agent readiness 50.2** — agent-ready (gated down from agent-native)

| Agent-readiness dimension | Result |
|---|---|
| Spec Presence | yes |
| Agentic Access | derived (partial credit) |
| Reversibility Documented | — |
| MCP Server | verified |
| Auth Clarity | served (partial credit) |
| Idempotency | — |
| Error Semantics | derived (partial credit) |
| OpenAPI Examples | — |
| Rate Limit Signal | documented (partial credit) |
| Event Surface Described | yes |
| Agent Skills | derived (partial credit) |
| Well Known Catalog | yes |
| Consent Identity | — |
| Agent Card | — |
| Dry Run Mode | — |
| Delegated Identity | served (partial credit) |
| Protected Resource Metadata | verified |
| Dynamic Client Registration | — |
| Agentic Commerce | — |
| Pagination Ratio | partial |
| Operation Distinctiveness | distinct |

**Regulatory alignment 45.1** — Horizontal (data, software, accessibility, platform)

The rubric is published at [github.com/api-evangelist/kin-score](https://github.com/api-evangelist/kin-score). The per-check breakdown behind these numbers — which checks were earned, partial or missed — along with score history and how this provider compares to its peers, is available at [apis.io/providers/sentry-system/](https://apis.io/providers/sentry-system/).

## How this profile is made

API Evangelist profiles a company's public API surface the way a developer would find it: the website, developer portal and documentation, the specifications it publishes, its public repositories, and its pricing, status and changelog pages. From that it derives the properties above — contracts split per API, workflows, schemas, collections, governance rules, plans and rate limits — and scores them against the published [Kin Score rubric](https://github.com/api-evangelist/kin-score).

The artifacts are published on [APIs.io](https://apis.io), not in this repository. This repository is the public record for Sentry: the place to say something is wrong, missing or out of date, and to ask for a re-score or a removal.

## Open an issue

- **Something about this listing** — a wrong description, a missing API, a stale artifact or a question about the rating: [open an issue on this repository](https://github.com/api-evangelist/sentry-system/issues/new).
- **A new API, or one that is not in the catalog yet** — [submit it on APIs.io](https://apis.io/add/).
- **Anything else** — [the APIs.io inbox](https://github.com/api-search/inbox/issues/new) or [info@apievangelist.com](mailto:info@apievangelist.com).
