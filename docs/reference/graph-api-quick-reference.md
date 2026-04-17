---
content_sources:
    - https://learn.microsoft.com/graph/api/resources/users
    - https://learn.microsoft.com/graph/api/resources/groups-overview
    - https://learn.microsoft.com/graph/api/resources/application
    - https://learn.microsoft.com/graph/api/resources/serviceprincipal
    - https://learn.microsoft.com/graph/api/resources/signin
---

# Microsoft Graph API Quick Reference

Use this page to quickly locate common Azure Entra ID Microsoft Graph endpoints and corresponding `az rest` patterns.

## Command Reference / Data Tables

### Request basics

| Pattern | Example |
| --- | --- |
| Base URL | `https://graph.microsoft.com/v1.0` |
| Beta URL | `https://graph.microsoft.com/beta` |
| Standard GET | `az rest --method get --url "https://graph.microsoft.com/v1.0/users"` |
| Standard POST | `az rest --method post --url "https://graph.microsoft.com/v1.0/applications" --headers "Content-Type=application/json" --body '{...}'` |
| Filter query | `?$filter=displayName eq '$DISPLAY_NAME'` |
| Projection query | `?$select=id,displayName,userPrincipalName` |
| Pagination hint | Use `@odata.nextLink` until all pages are collected |

### Users

| Operation | Endpoint | `az rest` example |
| --- | --- | --- |
| List users | `GET /users` | `az rest --method get --url "https://graph.microsoft.com/v1.0/users?$top=25"` |
| Get user by ID | `GET /users/{id}` | `az rest --method get --url "https://graph.microsoft.com/v1.0/users/$USER_ID"` |
| Select core fields | `GET /users?$select=id,displayName,userPrincipalName` | `az rest --method get --url "https://graph.microsoft.com/v1.0/users?$select=id,displayName,userPrincipalName"` |
| Filter by UPN | `GET /users?$filter=userPrincipalName eq '{upn}'` | `az rest --method get --url "https://graph.microsoft.com/v1.0/users?$filter=userPrincipalName eq '$UPN'"` |
| Create user | `POST /users` | `az rest --method post --url "https://graph.microsoft.com/v1.0/users" --headers "Content-Type=application/json" --body '{"accountEnabled":true,"displayName":"'$DISPLAY_NAME'","mailNickname":"'$DISPLAY_NAME'","userPrincipalName":"'$UPN'","passwordProfile":{"forceChangePasswordNextSignIn":true,"password":"<temporary-password>"}}'` |
| Update user | `PATCH /users/{id}` | `az rest --method patch --url "https://graph.microsoft.com/v1.0/users/$USER_ID" --headers "Content-Type=application/json" --body '{"displayName":"'$DISPLAY_NAME'"}'` |
| Delete user | `DELETE /users/{id}` | `az rest --method delete --url "https://graph.microsoft.com/v1.0/users/$USER_ID"` |
| Member groups | `POST /users/{id}/getMemberGroups` | `az rest --method post --url "https://graph.microsoft.com/v1.0/users/$USER_ID/getMemberGroups" --headers "Content-Type=application/json" --body '{"securityEnabledOnly":false}'` |

### Groups

| Operation | Endpoint | `az rest` example |
| --- | --- | --- |
| List groups | `GET /groups` | `az rest --method get --url "https://graph.microsoft.com/v1.0/groups?$top=25"` |
| Get group | `GET /groups/{id}` | `az rest --method get --url "https://graph.microsoft.com/v1.0/groups/$OBJECT_ID"` |
| Filter by display name | `GET /groups?$filter=displayName eq '{name}'` | `az rest --method get --url "https://graph.microsoft.com/v1.0/groups?$filter=displayName eq '$DISPLAY_NAME'"` |
| Create group | `POST /groups` | `az rest --method post --url "https://graph.microsoft.com/v1.0/groups" --headers "Content-Type=application/json" --body '{"displayName":"'$DISPLAY_NAME'","mailEnabled":false,"mailNickname":"'$DISPLAY_NAME'","securityEnabled":true}'` |
| Add member | `POST /groups/{id}/members/$ref` | `az rest --method post --url "https://graph.microsoft.com/v1.0/groups/$OBJECT_ID/members/\$ref" --headers "Content-Type=application/json" --body '{"@odata.id":"https://graph.microsoft.com/v1.0/directoryObjects/'$USER_ID'"}'` |
| Remove member | `DELETE /groups/{id}/members/{id}/$ref` | `az rest --method delete --url "https://graph.microsoft.com/v1.0/groups/$OBJECT_ID/members/$USER_ID/\$ref"` |
| Owners | `GET /groups/{id}/owners` | `az rest --method get --url "https://graph.microsoft.com/v1.0/groups/$OBJECT_ID/owners"` |
| Delete group | `DELETE /groups/{id}` | `az rest --method delete --url "https://graph.microsoft.com/v1.0/groups/$OBJECT_ID"` |

### Applications

| Operation | Endpoint | `az rest` example |
| --- | --- | --- |
| List apps | `GET /applications` | `az rest --method get --url "https://graph.microsoft.com/v1.0/applications?$top=25"` |
| Get app | `GET /applications/{id}` | `az rest --method get --url "https://graph.microsoft.com/v1.0/applications/$OBJECT_ID"` |
| Filter by appId | `GET /applications?$filter=appId eq '{appId}'` | `az rest --method get --url "https://graph.microsoft.com/v1.0/applications?$filter=appId eq '$APP_ID'"` |
| Create app | `POST /applications` | `az rest --method post --url "https://graph.microsoft.com/v1.0/applications" --headers "Content-Type=application/json" --body '{"displayName":"'$DISPLAY_NAME'"}'` |
| Update redirect URIs | `PATCH /applications/{id}` | `az rest --method patch --url "https://graph.microsoft.com/v1.0/applications/$OBJECT_ID" --headers "Content-Type=application/json" --body '{"web":{"redirectUris":["https://localhost/auth/callback"]}}'` |
| Add password | `POST /applications/{id}/addPassword` | `az rest --method post --url "https://graph.microsoft.com/v1.0/applications/$OBJECT_ID/addPassword" --headers "Content-Type=application/json" --body '{"passwordCredential":{"displayName":"client-secret-01"}}'` |
| Remove password | `POST /applications/{id}/removePassword` | `az rest --method post --url "https://graph.microsoft.com/v1.0/applications/$OBJECT_ID/removePassword" --headers "Content-Type=application/json" --body '{"keyId":"<password-key-id>"}'` |
| Delete app | `DELETE /applications/{id}` | `az rest --method delete --url "https://graph.microsoft.com/v1.0/applications/$OBJECT_ID"` |

### Service principals

| Operation | Endpoint | `az rest` example |
| --- | --- | --- |
| List service principals | `GET /servicePrincipals` | `az rest --method get --url "https://graph.microsoft.com/v1.0/servicePrincipals?$top=25"` |
| Get service principal | `GET /servicePrincipals/{id}` | `az rest --method get --url "https://graph.microsoft.com/v1.0/servicePrincipals/$OBJECT_ID"` |
| Filter by appId | `GET /servicePrincipals?$filter=appId eq '{appId}'` | `az rest --method get --url "https://graph.microsoft.com/v1.0/servicePrincipals?$filter=appId eq '$APP_ID'"` |
| Create service principal | `POST /servicePrincipals` | `az rest --method post --url "https://graph.microsoft.com/v1.0/servicePrincipals" --headers "Content-Type=application/json" --body '{"appId":"'$APP_ID'"}'` |
| App role assignments | `GET /servicePrincipals/{id}/appRoleAssignments` | `az rest --method get --url "https://graph.microsoft.com/v1.0/servicePrincipals/$OBJECT_ID/appRoleAssignments"` |
| Owners | `GET /servicePrincipals/{id}/owners` | `az rest --method get --url "https://graph.microsoft.com/v1.0/servicePrincipals/$OBJECT_ID/owners"` |
| Delete service principal | `DELETE /servicePrincipals/{id}` | `az rest --method delete --url "https://graph.microsoft.com/v1.0/servicePrincipals/$OBJECT_ID"` |

### Conditional Access and policy objects

| Operation | Endpoint | `az rest` example |
| --- | --- | --- |
| List policies | `GET /identity/conditionalAccess/policies` | `az rest --method get --url "https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies"` |
| Get policy | `GET /identity/conditionalAccess/policies/{id}` | `az rest --method get --url "https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies/$OBJECT_ID"` |
| List named locations | `GET /identity/conditionalAccess/namedLocations` | `az rest --method get --url "https://graph.microsoft.com/v1.0/identity/conditionalAccess/namedLocations"` |
| Auth strength policies | `GET /identity/conditionalAccess/authenticationStrength/policies` | `az rest --method get --url "https://graph.microsoft.com/v1.0/identity/conditionalAccess/authenticationStrength/policies"` |
| Update state | `PATCH /identity/conditionalAccess/policies/{id}` | `az rest --method patch --url "https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies/$OBJECT_ID" --headers "Content-Type=application/json" --body '{"state":"enabledForReportingButNotEnforced"}'` |

### Sign-in logs

| Operation | Endpoint | `az rest` example |
| --- | --- | --- |
| Recent sign-ins | `GET /auditLogs/signIns` | `az rest --method get --url "https://graph.microsoft.com/v1.0/auditLogs/signIns?$top=25"` |
| Filter by user | `GET /auditLogs/signIns?$filter=userId eq '{id}'` | `az rest --method get --url "https://graph.microsoft.com/v1.0/auditLogs/signIns?$filter=userId eq '$USER_ID'"` |
| Filter by app | `GET /auditLogs/signIns?$filter=appId eq '{appId}'` | `az rest --method get --url "https://graph.microsoft.com/v1.0/auditLogs/signIns?$filter=appId eq '$APP_ID'"` |
| Failed sign-ins | `GET /auditLogs/signIns?$filter=status/errorCode ne 0` | `az rest --method get --url "https://graph.microsoft.com/v1.0/auditLogs/signIns?$filter=status/errorCode ne 0"` |
| Select status fields | `GET /auditLogs/signIns?$select=id,createdDateTime,status,conditionalAccessStatus` | `az rest --method get --url "https://graph.microsoft.com/v1.0/auditLogs/signIns?$select=id,createdDateTime,status,conditionalAccessStatus"` |
| Risk details (beta) | `GET /beta/auditLogs/signIns?$filter=riskDetail ne 'none'` | `az rest --method get --url "https://graph.microsoft.com/beta/auditLogs/signIns?$filter=riskDetail ne 'none'"` |

### Audit logs

| Operation | Endpoint | `az rest` example |
| --- | --- | --- |
| Directory audits | `GET /auditLogs/directoryAudits` | `az rest --method get --url "https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$top=25"` |
| Provisioning logs | `GET /auditLogs/provisioning` | `az rest --method get --url "https://graph.microsoft.com/v1.0/auditLogs/provisioning?$top=25"` |
| Filter activity | `GET /auditLogs/directoryAudits?$filter=activityDisplayName eq '{name}'` | `az rest --method get --url "https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=activityDisplayName eq 'Add user'"` |
| Filter target | `GET /auditLogs/directoryAudits?$filter=targetResources/any(t:t/id eq '{id}')` | `az rest --method get --url "https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=targetResources/any(t:t/id eq '$OBJECT_ID')"` |

### Tenant metadata

| Operation | Endpoint | `az rest` example |
| --- | --- | --- |
| Organization profile | `GET /organization` | `az rest --method get --url "https://graph.microsoft.com/v1.0/organization"` |
| Verified domains | `GET /domains` | `az rest --method get --url "https://graph.microsoft.com/v1.0/domains"` |
| Subscribed SKUs | `GET /subscribedSkus` | `az rest --method get --url "https://graph.microsoft.com/v1.0/subscribedSkus"` |
| Devices | `GET /devices` | `az rest --method get --url "https://graph.microsoft.com/v1.0/devices?$top=25"` |

## Usage Notes

- Prefer `v1.0` endpoints for production automation unless a required capability is beta-only.
- Use `ConsistencyLevel: eventual` and `$count=true` only when an advanced query requires it.
- Escape `\$ref` in shell commands so the shell does not treat `$ref` as a variable.
- Store complex JSON request bodies in files when commands become hard to read or maintain.
- Expect pagination on large directories and capture `@odata.nextLink` in scripts.
- Log `request-id` and `client-request-id` values when troubleshooting Microsoft Graph failures with Microsoft support.

## See Also

- [Azure Entra ID CLI cheatsheet](./cli-cheatsheet.md)
- [Platform limits](./platform-limits.md)
- [Error codes](./error-codes.md)
- https://learn.microsoft.com/graph/use-the-api

## Sources

- https://learn.microsoft.com/graph/use-the-api
- https://learn.microsoft.com/graph/api/resources/users
- https://learn.microsoft.com/graph/api/resources/groups-overview
- https://learn.microsoft.com/graph/api/resources/application
- https://learn.microsoft.com/graph/api/resources/serviceprincipal
- https://learn.microsoft.com/graph/api/resources/conditionalaccessroot
- https://learn.microsoft.com/graph/api/resources/signin
- https://learn.microsoft.com/graph/api/resources/directoryaudit
