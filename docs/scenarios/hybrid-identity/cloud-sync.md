---
title: Configure Microsoft Entra Cloud Sync
content_sources:
  diagrams:
    - id: cloud-sync-agent-flow
      type: flowchart
      source: self-generated
      justification: "Synthesized from Microsoft Learn documentation"
      description: Cloud Sync provisioning agent architecture.
  sources:
    - title: What is Microsoft Entra Cloud Sync
      url: https://learn.microsoft.com/en-us/entra/identity/hybrid/cloud-sync/what-is-cloud-sync
    - title: Install Microsoft Entra provisioning agent
      url: https://learn.microsoft.com/en-us/entra/identity/hybrid/cloud-sync/how-to-install
---

# Configure Microsoft Entra Cloud Sync

This scenario explains the lighter-weight Cloud Sync model that uses provisioning agents and cloud-managed configuration instead of the full Entra Connect Sync server engine.

## Prerequisites

- An on-premises Active Directory environment.
- A supported server host for the provisioning agent.
- Microsoft Entra permissions to configure cloud provisioning.
- A pilot OU or group scope for first synchronization.

## Architecture

<!-- diagram-id: cloud-sync-agent-flow -->
```mermaid
flowchart LR
    A[On-premises AD DS] --> B[Provisioning agent]
    B --> C[Cloud Sync configuration]
    C --> D[Microsoft Entra provisioning service]
    D --> E[Microsoft Entra ID objects]
```

## Step-by-Step Configuration

1. Confirm the target tenant and service readiness.

    ```bash
    az login
    az account show --output table
    az rest --method GET --uri "https://graph.microsoft.com/v1.0/organization"
    ```

2. Prepare the agent host.

    - Join the server to the domain if required by your design.
    - Allow outbound connectivity required by the provisioning agent.
    - Limit local administrative access to the host.

3. Install the Microsoft Entra provisioning agent and register it to the tenant.

    During setup, record:

    - The tenant you registered to.
    - The service account or credential used by the agent.
    - The pilot scope you plan to enable first.

4. Review provisioning service principals if needed for automation visibility.

    ```bash
    az rest \
        --method GET \
        --uri "https://graph.microsoft.com/v1.0/servicePrincipals?$filter=displayName eq 'Azure AD Cloud Sync'"
    ```

5. Configure the cloud sync job in Microsoft Entra admin center.

    - Select the connected forest.
    - Choose OU or group scoping.
    - Map required attributes.
    - Enable password hash synchronization if supported and desired for the scenario.

6. Verify synchronized objects in Microsoft Graph.

    ```bash
    az rest \
        --method GET \
        --uri "https://graph.microsoft.com/v1.0/users?$filter=onPremisesSyncEnabled eq true"
    ```

7. Validate pilot provisioning behavior.

    - Create or update a pilot user on-premises.
    - Wait for the provisioning cycle.
    - Confirm the object appears or updates in Microsoft Entra ID.

8. Check group flow for pilot groups.

    ```bash
    az rest \
        --method GET \
        --uri "https://graph.microsoft.com/v1.0/groups?$filter=onPremisesSyncEnabled eq true"
    ```

9. Monitor agent and provisioning status.

    - Review agent health in the admin center.
    - Check provisioning logs for skipped or failed objects.
    - Keep the scope narrow until mappings and filters behave as expected.

10. Expand rollout once pilot provisioning is stable.

    - Add more OUs or groups.
    - Revalidate attribute mappings.
    - Confirm operational ownership for the agent host and sync job.

## Verification

- The provisioning agent is registered and healthy.
- Pilot users and groups appear in Microsoft Entra ID.
- `onPremisesSyncEnabled` is set for synchronized objects.
- Provisioning logs show successful create and update actions for pilot objects.

## Common Issues

| Issue | What it usually means | Fix |
|---|---|---|
| Agent offline | Host connectivity or service health problem. | Restore outbound connectivity, restart the agent service, and recheck registration. |
| Objects not provisioning | Scope, mapping, or permissions are incomplete. | Review OU or group selection, attribute mappings, and connector permissions. |
| Unexpected attribute values | Default mappings do not match your directory design. | Adjust mapping rules before widening the rollout. |
| Too many pilot objects | Scope filters are broader than intended. | Reduce scoping and rerun pilot validation. |
| Admin confusion between sync models | Cloud Sync and Entra Connect assumptions were mixed. | Document which sync engine owns the workload and avoid unsupported overlap. |

## See Also

- [Hybrid Identity Scenarios](index.md)
- [Entra Connect Sync](entra-connect-sync.md)
- [Operations: User Lifecycle Management](../../operations/user-lifecycle-management.md)
- [Troubleshooting: Sync Errors in Hybrid Identity](../../troubleshooting/playbooks/sync-errors-hybrid-identity.md)

## Sources

- https://learn.microsoft.com/en-us/entra/identity/hybrid/cloud-sync/what-is-cloud-sync
- https://learn.microsoft.com/en-us/entra/identity/hybrid/cloud-sync/how-to-install
- https://learn.microsoft.com/en-us/entra/identity/hybrid/cloud-sync/how-to-configure
