---
content_sources:
    - https://learn.microsoft.com/entra/identity/users/directory-service-limits-restrictions
    - https://learn.microsoft.com/entra/identity/users/groups-service-limits
    - https://learn.microsoft.com/entra/identity/enterprise-apps/add-application-portal
---

# Azure Entra ID Platform Limits

This page summarizes commonly referenced Azure Entra ID service limits that influence identity architecture, automation scale, and operational guardrails.

## Command Reference / Data Tables

### Core directory limits

| Area | Limit or behavior | Planning note |
| --- | --- | --- |
| Objects in a tenant | Depends on edition and backend capacity; very large enterprise tenants are supported | Validate current published tenant scale guidance before migrations.
| Administrative units | Supported for scoped administration, with tenant-specific practical management limits | Use them to reduce blast radius rather than as a hierarchy substitute.
| Verified domains | Multiple custom domains supported per tenant | Track domain verification dependencies during mergers and cutovers.
| Directory roles | Fixed built-in roles plus custom roles in supported SKUs | Favor least privilege and role-assignable groups where possible.

### User and group limits

| Area | Limit or behavior | Planning note |
| --- | --- | --- |
| Group memberships per user | A user can be a member of many groups, but token issuance and app claims limits still apply | Keep token bloat in mind for legacy apps.
| Owners per group | Multiple owners supported | Always assign at least two owners for operational resilience.
| Dynamic groups | Rule processing time varies with tenant size and change volume | Avoid assuming immediate membership convergence.
| Nested groups | Supported for many authorization scenarios, but not every Microsoft 365 workload evaluates nesting the same way | Test workload-specific behavior before relying on it.
| Role-assignable groups | Special restrictions apply, including elevated protection and supported role mappings | Reserve them for privileged access administration.

### Application and service principal limits

| Area | Limit or behavior | Planning note |
| --- | --- | --- |
| App registrations per user | Controlled by tenant settings and defaults | Restrict self-service creation in regulated environments.
| Service principals | One service principal per tenant per app instance | Multi-tenant apps create separate service principals in each tenant.
| Redirect URIs | Platform-specific maximums apply | Standardize URI patterns to stay under platform caps.
| Client secrets and certificates | Multiple credentials supported concurrently | Use overlap windows for rotation.
| App roles and scopes | Practical limits apply to manifest size and claim design | Keep manifests intentionally small.

### Token and claim-related boundaries

| Area | Limit or behavior | Planning note |
| --- | --- | --- |
| Group claims in tokens | Large group memberships can trigger overage behavior | Build apps to handle overage claims with Graph lookups.
| Token size | Proxies, browsers, and apps may impose effective size limits before Entra ID does | Keep optional claims minimal.
| Access token lifetime | Governed by service defaults and supported policy controls | Do not hard-code assumptions in clients.
| Conditional Access evaluation | Per-request evaluation adds control, not infinite branching capacity | Simplify policy sets to keep troubleshooting manageable.

### Reporting and log retention

| Area | Limit or behavior | Planning note |
| --- | --- | --- |
| Sign-in logs retention | Retention depends on license level and export destination | Export to Log Analytics, Event Hubs, or storage for longer retention.
| Audit logs retention | Similar license and export considerations apply | Align retention design with compliance requirements.
| Microsoft Graph paging | Large result sets are paged | Automation must follow `@odata.nextLink`.
| Throttling | Graph and portal operations can be throttled | Add retries with backoff for bulk operations.

### Operational constraints

| Area | Limit or behavior | Planning note |
| --- | --- | --- |
| Soft-delete windows | Users, groups, and apps can have recovery windows depending on object type | Document recovery runbooks in advance.
| B2B invitations | External collaboration has tenant policy and cross-tenant setting dependencies | Test guest flows with a pilot tenant.
| Cross-tenant sync | Sync scale and attribute mappings must follow service guidance | Model joins, leavers, and attribute conflicts early.
| Privileged Identity Management | Activation and approval workflows depend on licensing and role scope | Build emergency access paths outside routine approval chains.

## Usage Notes

- Treat this page as a design checkpoint, not a substitute for the latest Microsoft Learn limits page.
- Reconfirm published numbers before migration waves, tenant consolidations, or identity governance rollouts.
- Distinguish between hard service limits and practical limits caused by tokens, UX, or downstream application behavior.
- For high-scale automation, design for Microsoft Graph throttling, pagination, and eventual consistency.
- When limits are unclear or edition-dependent, document the tenant SKU and test results alongside the implementation.

## See Also

- [Graph API quick reference](./graph-api-quick-reference.md)
- [Error codes](./error-codes.md)
- https://learn.microsoft.com/entra/identity/users/directory-service-limits-restrictions

## Sources

- https://learn.microsoft.com/entra/identity/users/directory-service-limits-restrictions
- https://learn.microsoft.com/entra/identity/users/groups-service-limits
- https://learn.microsoft.com/entra/identity/enterprise-apps/add-application-portal
- https://learn.microsoft.com/entra/identity/authentication/concept-authentication-strengths
- https://learn.microsoft.com/graph/throttling
