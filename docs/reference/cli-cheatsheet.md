---
content_sources:
    - https://learn.microsoft.com/cli/azure/ad
    - https://learn.microsoft.com/cli/azure/account
    - https://learn.microsoft.com/graph/api/overview
    - https://learn.microsoft.com/entra/identity/conditional-access/
---

# Azure Entra ID CLI Cheatsheet

Use these long-form Azure CLI and `az rest` examples as a quick operational reference for common Azure Entra ID tasks.

## Command Reference / Data Tables

### Session and tenant context

```bash
az login --tenant "$TENANT_ID"
az account show --output table
az account list --all --output table
az account set --subscription "<subscription-name-or-id>"
az account get-access-token --resource-type ms-graph
az account get-access-token --resource https://graph.microsoft.com/
```

### User management

```bash
az ad user list --output table
az ad user show --id "$UPN" --output json
az ad user show --id "$USER_ID" --query "{id:id,displayName:displayName,userPrincipalName:userPrincipalName}"
az ad user create --display-name "$DISPLAY_NAME" --password "<temporary-password>" --user-principal-name "$UPN" --force-change-password-next-sign-in true
az ad user update --id "$USER_ID" --display-name "$DISPLAY_NAME"
az ad user update --id "$USER_ID" --account-enabled false
az ad user update --id "$USER_ID" --account-enabled true
az ad user delete --id "$USER_ID"
az ad user get-member-groups --id "$USER_ID"
az ad user list --filter "startsWith(displayName,'$DISPLAY_NAME')" --output table
az ad user list --filter "userPrincipalName eq '$UPN'" --output json
az ad user show --id "$UPN" --query "assignedLicenses"
```

### Group management

```bash
az ad group list --output table
az ad group show --group "$DISPLAY_NAME" --output json
az ad group show --group "$OBJECT_ID" --query "{id:id,displayName:displayName,securityEnabled:securityEnabled}"
az ad group create --display-name "$DISPLAY_NAME" --mail-nickname "$DISPLAY_NAME"
az ad group delete --group "$OBJECT_ID"
az ad group member list --group "$OBJECT_ID" --output table
az ad group member add --group "$OBJECT_ID" --member-id "$USER_ID"
az ad group member remove --group "$OBJECT_ID" --member-id "$USER_ID"
az ad group owner list --group "$OBJECT_ID" --output table
az ad group owner add --group "$OBJECT_ID" --owner-object-id "$USER_ID"
az ad group owner remove --group "$OBJECT_ID" --owner-object-id "$USER_ID"
az ad group list --filter "displayName eq '$DISPLAY_NAME'" --output json
az ad group member check --group "$OBJECT_ID" --member-id "$USER_ID"
```

### App registration management

```bash
az ad app list --output table
az ad app show --id "$APP_ID" --output json
az ad app create --display-name "$DISPLAY_NAME"
az ad app update --id "$APP_ID" --display-name "$DISPLAY_NAME"
az ad app update --id "$APP_ID" --web-redirect-uris "https://localhost/auth/callback"
az ad app update --id "$APP_ID" --enable-id-token-issuance true
az ad app update --id "$APP_ID" --enable-access-token-issuance true
az ad app credential list --id "$APP_ID" --output table
az ad app credential reset --id "$APP_ID" --append --display-name "client-secret-01"
az ad app permission list --id "$APP_ID" --output table
az ad app permission add --id "$APP_ID" --api 00000003-0000-0000-c000-000000000000 --api-permissions 62a82d76-70ea-41e2-9197-370581804d09=Scope
az ad app permission grant --id "$APP_ID" --api 00000003-0000-0000-c000-000000000000 --scope User.Read.All
az ad app permission admin-consent --id "$APP_ID"
az ad app delete --id "$APP_ID"
```

### Service principal management

```bash
az ad sp list --output table
az ad sp show --id "$APP_ID" --output json
az ad sp create --id "$APP_ID"
az ad sp update --id "$OBJECT_ID" --set accountEnabled=false
az ad sp update --id "$OBJECT_ID" --set accountEnabled=true
az ad sp credential list --id "$OBJECT_ID" --output table
az ad sp credential reset --id "$OBJECT_ID" --append --display-name "rotation-secret-01"
az ad sp owner list --id "$OBJECT_ID" --output table
az ad sp owner add --id "$OBJECT_ID" --owner-object-id "$USER_ID"
az ad sp owner remove --id "$OBJECT_ID" --owner-object-id "$USER_ID"
az ad sp delete --id "$OBJECT_ID"
az ad sp list --filter "displayName eq '$DISPLAY_NAME'" --output json
az ad sp show --id "$APP_ID" --query "{id:id,appId:appId,displayName:displayName,servicePrincipalType:servicePrincipalType}"
```

### Conditional Access policy queries

```bash
az rest --method get --url "https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies"
az rest --method get --url "https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies?$select=id,displayName,state"
az rest --method get --url "https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies/$OBJECT_ID"
az rest --method get --url "https://graph.microsoft.com/v1.0/identity/conditionalAccess/namedLocations"
az rest --method get --url "https://graph.microsoft.com/v1.0/identity/conditionalAccess/authenticationStrength/policies"
az rest --method get --url "https://graph.microsoft.com/beta/identity/conditionalAccess/templates"
az rest --method patch --url "https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies/$OBJECT_ID" --headers "Content-Type=application/json" --body '{"state":"enabledForReportingButNotEnforced"}'
```

### Sign-in logs

```bash
az rest --method get --url "https://graph.microsoft.com/v1.0/auditLogs/signIns?$top=10"
az rest --method get --url "https://graph.microsoft.com/v1.0/auditLogs/signIns?$filter=userId eq '$USER_ID'"
az rest --method get --url "https://graph.microsoft.com/v1.0/auditLogs/signIns?$filter=appId eq '$APP_ID'"
az rest --method get --url "https://graph.microsoft.com/v1.0/auditLogs/signIns?$filter=status/errorCode ne 0"
az rest --method get --url "https://graph.microsoft.com/v1.0/auditLogs/signIns?$select=id,createdDateTime,userDisplayName,appDisplayName,conditionalAccessStatus,status"
az rest --method get --url "https://graph.microsoft.com/beta/auditLogs/signIns?$filter=riskDetail ne 'none'"
az rest --method get --url "https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$top=10"
```

### Directory role lookups

```bash
az rest --method get --url "https://graph.microsoft.com/v1.0/directoryRoles"
az rest --method get --url "https://graph.microsoft.com/v1.0/directoryRoleTemplates"
az rest --method get --url "https://graph.microsoft.com/v1.0/directoryRoles/$OBJECT_ID/members"
az rest --method post --url "https://graph.microsoft.com/v1.0/directoryRoles" --headers "Content-Type=application/json" --body '{"roleTemplateId":"62e90394-69f5-4237-9190-012177145e10"}'
```

### Device and identity reporting

```bash
az rest --method get --url "https://graph.microsoft.com/v1.0/devices?$top=25"
az rest --method get --url "https://graph.microsoft.com/v1.0/devices?$filter=accountEnabled eq true"
az rest --method get --url "https://graph.microsoft.com/v1.0/organization"
az rest --method get --url "https://graph.microsoft.com/v1.0/domains"
az rest --method get --url "https://graph.microsoft.com/v1.0/subscribedSkus"
```

### Query formatting helpers

```bash
az ad user list --query "[].{displayName:displayName,upn:userPrincipalName,id:id}" --output table
az ad group list --query "[].{displayName:displayName,id:id,mailNickname:mailNickname}" --output table
az ad app list --query "[].{displayName:displayName,appId:appId,id:id}" --output table
az rest --method get --url "https://graph.microsoft.com/v1.0/users?$top=5" --query "value[].{id:id,displayName:displayName,userPrincipalName:userPrincipalName}"
```

## Usage Notes

- Run `az login --tenant "$TENANT_ID"` before executing tenant-scoped commands.
- Use object IDs for unambiguous updates and deletes when duplicate display names exist.
- For Microsoft Graph endpoints, confirm the signed-in identity has the required delegated or application permissions.
- Quote filter values to avoid shell expansion and malformed OData expressions.
- Conditional Access write operations should be tested in report-only mode before enforcement.
- Sign-in and audit log retention varies by license and export strategy; integrate with Log Analytics or SIEM if longer history is required.
- `az ad` commands remain widely used, but many advanced Entra ID scenarios now require `az rest` with Microsoft Graph.

## See Also

- [Graph API quick reference](./graph-api-quick-reference.md)
- [Platform limits](./platform-limits.md)
- [Error codes](./error-codes.md)
- https://learn.microsoft.com/cli/azure/ad
- https://learn.microsoft.com/graph/api/overview

## Sources

- https://learn.microsoft.com/cli/azure/ad
- https://learn.microsoft.com/cli/azure/account
- https://learn.microsoft.com/graph/api/user-list
- https://learn.microsoft.com/graph/api/group-list
- https://learn.microsoft.com/graph/api/application-list
- https://learn.microsoft.com/graph/api/serviceprincipal-list
- https://learn.microsoft.com/graph/api/resources/conditionalaccessroot
- https://learn.microsoft.com/graph/api/signin-list
- https://learn.microsoft.com/graph/api/directoryaudit-list
