---
content_sources:
    - https://learn.microsoft.com/entra/fundamentals/
    - https://learn.microsoft.com/graph/overview
    - https://learn.microsoft.com/cli/azure/
---

# Reference

Use this section when you need fast access to Azure Entra ID commands, API patterns, limits, and troubleshooting data while working through the guide.

## Command Reference / Data Tables

### CLI and automation

- [Azure CLI cheatsheet](./cli-cheatsheet.md) for day-to-day tenant, user, group, app, and log queries.
- [Microsoft Graph API quick reference](./graph-api-quick-reference.md) for REST endpoints and `az rest` examples.

### Platform behavior and limits

- [Platform limits](./platform-limits.md) for service boundaries, quotas, and design constraints.
- [Error codes](./error-codes.md) for common AADSTS sign-in and token issuance failures.

### Contribution workflow

- [Contributing guide](../contributing/index.md) for repository standards, diagrams, and content quality expectations.

## Usage Notes

- Prefer Microsoft Graph for current automation patterns when Azure AD Graph examples appear in older content.
- Use long-form Azure CLI flags to keep commands readable and self-documenting.
- Replace placeholders such as `$TENANT_ID`, `$USER_ID`, `$APP_ID`, `$OBJECT_ID`, `$UPN`, and `$DISPLAY_NAME` before running examples.
- Validate tenant-specific limits and preview feature behavior in Microsoft Learn before production rollout.

## See Also

- [CLI cheatsheet](./cli-cheatsheet.md)
- [Graph API quick reference](./graph-api-quick-reference.md)
- [Platform limits](./platform-limits.md)
- [Error codes](./error-codes.md)
- [Contributing](../contributing/index.md)

## Sources

- https://learn.microsoft.com/entra/fundamentals/
- https://learn.microsoft.com/graph/overview
- https://learn.microsoft.com/cli/azure/
