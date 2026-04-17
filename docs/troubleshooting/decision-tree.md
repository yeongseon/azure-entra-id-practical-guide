---
title: Troubleshooting Decision Tree
description: Large decision tree for user sign-in failures across MFA, Conditional Access, app configuration, and account state branches.
content_sources:
    - title: Microsoft Entra sign-in logs overview
      url: https://learn.microsoft.com/en-us/entra/identity/monitoring-health/concept-sign-ins
      sections:
        - Sign-in analysis
        - Conditional Access result interpretation
    - title: Microsoft Entra Conditional Access overview
      url: https://learn.microsoft.com/en-us/entra/identity/conditional-access/overview
      sections:
        - Policy decision factors
        - Access controls
    - title: Diagram metadata
      diagram_metadata:
        - id: user-cannot-sign-in-tree
          description: Decision tree for user cannot sign in across user state, MFA, CA, and app configuration branches.
---

# Troubleshooting Decision Tree

Use this page when the report is still broad: “the user cannot sign in.” The goal is not to solve everything from one chart. The goal is to route the issue to the right control plane quickly.

Start with the affected identity, app, and a timestamp. Then walk the tree from top to bottom.

<!-- diagram-id: user-cannot-sign-in-tree -->
```mermaid
flowchart TD
    A[User cannot sign in] --> B{Is there a matching sign-in log event?}
    B -- No --> C{Did the app return a client-side or browser error before redirect?}
    C -- Yes --> D[Check app configuration: redirect URI, authority, client ID, network path]
    C -- No --> E[Check client path, federation path, and whether the request reached Entra ID]
    B -- Yes --> F{Did primary authentication succeed?}
    F -- No --> G{What failed first?}
    G -- Account issue --> H[Check accountEnabled, password state, lockout, sync freshness, guest redemption state]
    G -- Credential issue --> I[Check password reset, federation dependency, smart lockout indicators]
    F -- Yes --> J{Did MFA or authentication strength fail?}
    J -- Yes --> K[Check registered methods, method usability, Temporary Access Pass, Authenticator state]
    J -- No --> L{Did Conditional Access block or interrupt?}
    L -- Yes --> M[Review CA result, targeted policies, device compliance, location, risk, exclusions]
    L -- No --> N{Did the app or consent step fail?}
    N -- Yes --> O[Check enterprise app, user assignment, consent settings, delegated permissions, admin approval flow]
    N -- No --> P{Was a token issued?}
    P -- No --> Q[Check authority, scopes, tenant mode, credentials, claims requests, service principal state]
    P -- Yes --> R[Check downstream authorization or resource-side access controls]
    H --> S[Use sign-in failure or guest access playbook]
    I --> S
    K --> T[Use MFA registration issues playbook]
    M --> U[Use Conditional Access unexpected block playbook]
    O --> V[Use app permission consent issues playbook]
    Q --> W[Use token issuance failure playbook]
    R --> X[Validate non-Entra authorization boundary]
```

## Step 1: Verify That the Attempt Reached Entra ID

If there is no matching sign-in event, do not spend the next 30 minutes reading Conditional Access policies. The request may never have reached Entra ID. That usually means a client-side, authority, redirect, network, or browser flow problem.

Good first check:

```bash
az rest --method get --url "https://graph.microsoft.com/v1.0/auditLogs/signIns?$filter=userId eq '$USER_ID'&$top=5"
```

## Step 2: Distinguish Primary Authentication Failure From Post-Authentication Failure

If primary authentication fails, focus on the account, credential, or federation path.

If primary authentication succeeds, stop blaming the password and move to MFA, Conditional Access, consent, or token issuance.

## Step 3: Branch on the Most Specific Evidence

Use the most specific branch that matches the log result:

- User object disabled or stale.
- MFA required but unavailable.
- Conditional Access policy block.
- App consent or assignment failure.
- Token issuance failure.

Avoid branching only on the user-visible wording. The sign-in log is more precise.

## Quick Interpretation Guide

| Decision point | What it tells you | Typical next action |
|---|---|---|
| No sign-in event | Problem may be before Entra processing | Check app authority, redirect, browser, network |
| Primary auth failed | Identity or credential path issue | Confirm user state and authentication path |
| MFA failed | Method readiness issue or policy requirement mismatch | Inspect authentication methods and MFA requirement source |
| Conditional Access failed | Policy made the final block decision | Read CA results and policy targeting |
| Consent failed | App governance blocked access | Review service principal, permission grant model, consent rules |
| Token not issued | Protocol or app identity problem | Review scopes, tenant endpoint, credentials, claims |

## Minimal Commands by Branch

### User state

```bash
az ad user show --id "$USER_ID"
az rest --method get --url "https://graph.microsoft.com/v1.0/users/$USER_ID?$select=id,accountEnabled,userType,onPremisesSyncEnabled"
```

### MFA

```bash
az rest --method get --url "https://graph.microsoft.com/v1.0/users/$USER_ID/authentication/methods"
```

### Conditional Access

```bash
az rest --method get --url "https://graph.microsoft.com/v1.0/auditLogs/signIns?$filter=correlationId eq '$CORRELATION_ID'"
```

### App configuration and consent

```bash
az rest --method get --url "https://graph.microsoft.com/v1.0/servicePrincipals?$filter=appId eq '$APP_ID'"
az rest --method get --url "https://graph.microsoft.com/v1.0/applications?$filter=appId eq '$APP_ID'"
```

## Escalation Rules

Move to a full playbook immediately if:

- The same branch affects multiple users.
- The evidence is contradictory.
- The mitigation requires policy changes.
- Hybrid sync, cross-tenant access, or workload identity is involved.

## See Also

- [Troubleshooting Overview](index.md)
- [First 10 Minutes](first-10-minutes/index.md)
- [Sign-in Failure Investigation](playbooks/sign-in-failure-investigation.md)
- [Conditional Access Unexpected Block](playbooks/conditional-access-unexpected-block.md)
- [Token Issuance Failure](playbooks/token-issuance-failure.md)

## Sources

- https://learn.microsoft.com/en-us/entra/identity/monitoring-health/concept-sign-ins
- https://learn.microsoft.com/en-us/entra/identity/conditional-access/overview
- https://learn.microsoft.com/en-us/entra/identity/authentication/concept-authentication-methods-manage
