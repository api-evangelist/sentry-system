---
name: sentry-invite-member-and-onboard
description: Invite a user into a Sentry organization and put them on a team so they can see the right projects.
api: Sentry Web API (https://sentry.io/api/0)
generated: '2026-09-18'
method: generated
source: arazzo/sentry-system-invite-member-onboard-team-workflow.yml + openapi/sentry-system-members-api-openapi.yml
operations:
  - addOrganizationMember
  - retrieveOrganizationMember
  - addMemberToTeam
  - listTeamMembers
---

# Invite a member and onboard them to a team

Sentry's access model is two-step: organization membership grants a role, team membership grants
project visibility. Doing only the first leaves a user who can log in and see nothing.

## Steps

1. **Invite** — `addOrganizationMember` → `POST /organizations/{organization_id_or_slug}/members/`.
   Supply `email` and `orgRole`. This sends an invitation; it does not create an active user.
2. **Read it back** — `retrieveOrganizationMember` →
   `GET /organizations/{organization_id_or_slug}/members/{member_id}/`. Keep the `member_id`.
3. **Add to a team** — `addMemberToTeam` →
   `POST /organizations/{organization_id_or_slug}/members/{member_id}/teams/{team_id_or_slug}/`.
4. **Verify** — `listTeamMembers` → `GET /teams/{organization_id_or_slug}/{team_id_or_slug}/members/`.

## Rules an agent must follow

- **This grants access to production error data,** which routinely contains user identifiers and
  request context. Never run this flow on an email address the user did not explicitly supply, and
  never widen `orgRole` beyond what was asked.
- **Reversal:** `removeMemberFromTeam` reverses step 3 and `deleteOrganizationMember` reverses step 1.
  Both are documented, neither has a stated window — a removed member is removed, not archived.
- **Enterprise organizations may be SCIM-provisioned.** Where SCIM is in use
  (`openapi/sentry-system-scim-members-api-openapi.yml`), the identity provider is the source of truth
  and a direct invite will be reconciled away. Check before inviting.
- Required scopes: `member:write` for both writes, `member:read` to read back.
