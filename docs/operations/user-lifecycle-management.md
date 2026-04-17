---
title: User Lifecycle Management
content_sources:
    - https://learn.microsoft.com/entra/fundamentals/
    - https://learn.microsoft.com/graph/api/resources/users
    - https://learn.microsoft.com/cli/azure/ad/user
---

# User Lifecycle Management

User lifecycle management in Microsoft Entra ID covers creating accounts, updating profile attributes, disabling access, deleting objects, and managing guest users. A reliable lifecycle process reduces stale access, improves auditability, and keeps downstream applications aligned with identity changes.

## Prerequisites

- Azure CLI authenticated to the target tenant.
- User Administrator or higher delegated rights.
- Variables available for scripts:
    - `TENANT_ID`
    - `USER_ID`
    - `DISPLAY_NAME`
    - `UPN`

!!! note
    Use generic examples and non-production test users during procedure validation. Do not place real personal data in command history or screenshots.

## When to Use

Use this workflow when you need to:

- create new cloud-only users;
- update names or usage details;
- disable accounts during leave or investigation;
- delete accounts after retention checks;
- process bulk lifecycle events; or
- govern guest invitations and removals.

## Procedure

### Step 1: Create a new user

Create a user with explicit long flags.

```bash
az ad user create \
    --display-name "$DISPLAY_NAME" \
    --user-principal-name "$UPN" \
    --password "TemporaryPassword123!" \
    --force-change-password-next-login true
```

Expected output includes the new user's object metadata such as `objectId` or `id`, display name, and user principal name. Record the resulting identifier in your change log.

This step establishes the identity record used by later license, group, and access assignment workflows.

### Step 2: Update user attributes

Update the user when the display name or other profile values change.

```bash
az ad user update \
    --id "$USER_ID" \
    --display-name "$DISPLAY_NAME"
```

Expected output is typically silent success or a returned user object depending on CLI version. Verify the new value afterward.

Updating attributes promptly helps preserve directory accuracy, especially for access reviews, approvals, and email-address-based workflows.

### Step 3: Disable sign-in access

Block sign-in without deleting the user so that memberships, ownership, and audit history remain intact.

```bash
az rest --method PATCH \
    --url "https://graph.microsoft.com/v1.0/users/$USER_ID" \
    --headers "Content-Type=application/json" \
    --body '{"accountEnabled":false}'
```

Expected output is an HTTP success status with no large payload. The user object should reflect `accountEnabled` as `false` when queried.

This is the preferred immediate offboarding action when legal hold, manager approval, or workload transfer is still pending.

### Step 4: Delete a user after retention checks

Delete only after mailbox, file ownership, device, and application dependencies have been reviewed.

```bash
az ad user delete --id "$USER_ID"
```

Expected output is silent success. The object moves into soft-delete behavior managed by Microsoft Entra and related workloads, subject to service retention rules.

Deletion should be the final lifecycle stage, not the first response to a departure.

### Step 5: Perform bulk updates

For bulk operations, iterate through a prepared CSV or JSON list and call the same long-flag commands per record.

```bash
while IFS=',' read -r UPN DISPLAY_NAME; do
    az ad user create \
        --display-name "$DISPLAY_NAME" \
        --user-principal-name "$UPN" \
        --password "TemporaryPassword123!" \
        --force-change-password-next-login true
done < users.csv
```

Expected output is one result per input row. Capture failures separately so that retries do not duplicate successful records.

Bulk processing should include input validation, duplicate detection, and an exception report.

### Step 6: Manage guest lifecycle

List guest accounts, review usage, then disable or delete guests that no longer require access.

```bash
az rest --method GET \
    --url "https://graph.microsoft.com/v1.0/users?$filter=userType eq 'Guest'&$select=id,displayName,userPrincipalName,accountEnabled"
```

Expected output is a guest user list with identifiers and status. Use it to decide whether to disable access first or proceed to deletion.

Guest lifecycle should align with sponsor review, expiration, and external collaboration policy.

<!-- diagram-id: user-lifecycle-flow -->
```mermaid
flowchart LR
    A[Create User] --> B[Update Attributes]
    B --> C[Assign Access]
    C --> D{Lifecycle Event}
    D --> E[Disable Sign-in]
    E --> F[Retention Review]
    F --> G[Delete User]
    D --> H[Guest Review]
    H --> E
```

## Verification

Run targeted checks after each operation.

```bash
az ad user show --id "$USER_ID"
az rest --method GET --url "https://graph.microsoft.com/v1.0/users/$USER_ID?$select=id,displayName,userPrincipalName,accountEnabled"
```

Confirm that:

- created users exist with the expected UPN;
- updated attributes are visible;
- disabled users show `accountEnabled` as `false`; and
- deleted users no longer appear in active user queries.

## Rollback / Troubleshooting

- If user creation fails, confirm domain verification and password complexity requirements.
- If update commands fail, verify the object ID and delegated role scope.
- If deletion was premature, recover using the appropriate restore workflow from deleted users if still available.
- If guest cleanup is blocked, review sponsorship, access package, or app assignment dependencies.

!!! warning
    Disabling accounts is safer than immediate deletion during incident response because it preserves the user object for investigation and handoff.

## Automation

- Use scripted bulk import jobs for onboarding waves.
- Trigger disable actions from HR departure events.
- Export guest inventories on a schedule for sponsor review.
- Store lifecycle evidence in a ticketing system with timestamps and operator IDs.

## See Also

- [Operations Overview](index.md)
- [Group Management](group-management.md)
- [Audit Log Analysis](audit-log-analysis.md)
- [Sign-in Log Analysis](sign-in-log-analysis.md)

## Sources

- Microsoft Learn: Azure CLI `az ad user`
- Microsoft Graph documentation for user resources
- Microsoft Entra fundamentals and external identities guidance
