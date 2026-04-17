---
content_sources:
    - https://learn.microsoft.com/entra/identity-platform/reference-error-codes
    - https://learn.microsoft.com/entra/identity/conditional-access/troubleshoot-conditional-access
    - https://learn.microsoft.com/graph/errors
---

# Azure Entra ID Error Codes

Use this page to quickly interpret common Azure Entra ID and AADSTS sign-in failures, token issues, and consent errors.

## Command Reference / Data Tables

### Sign-in and token issuance errors

| Error code | Typical meaning | Common cause | Resolution steps |
| --- | --- | --- | --- |
| AADSTS50011 | Reply URL mismatch | Redirect URI in the request does not exactly match the app registration | Verify the redirect URI scheme, host, path, and trailing slash. Update the app registration and retest. |
| AADSTS50020 | User account from identity provider does not exist in tenant | Wrong tenant, unsupported guest flow, or user not invited | Confirm the authority URL, tenant selection, and guest invitation state. Use the correct tenant endpoint. |
| AADSTS50034 | User account not found | UPN typo, stale username, or deleted account | Validate `$UPN`, confirm the user exists, and check soft-delete or sync status. |
| AADSTS50053 | Account is locked | Smart lockout or high-risk detection blocked the sign-in | Wait for unlock window or follow admin account recovery procedures. Review risky sign-ins. |
| AADSTS50055 | Password expired | User must change password before continuing | Complete password reset or self-service password reset, then retry sign-in. |
| AADSTS50056 | Password missing or empty | Unsupported auth flow or malformed request | Recheck the auth flow and client configuration. Avoid resource owner password flows unless explicitly required. |
| AADSTS50057 | User disabled | Account disabled in the directory | Re-enable the account if appropriate and investigate why it was disabled. |
| AADSTS50058 | Silent sign-in failed | No active session or cookies unavailable | Fall back to interactive sign-in and verify browser cookie settings. |
| AADSTS50059 | Tenant not identified | Authority URL missing or invalid tenant context | Use a tenant-specific or supported authority URL and confirm request parameters. |
| AADSTS65001 | User or admin has not consented | Required permissions were not granted | Trigger user consent where allowed or obtain admin consent for the app. |
| AADSTS65004 | User declined consent | Requested permissions were refused | Reduce permission scope if possible or guide the user through the approved consent process. |
| AADSTS650056 | Misconfigured application | App manifest, permissions, or resources are inconsistent | Review application registration, exposed scopes, and API permission configuration. |

### Conditional Access and MFA errors

| Error code | Typical meaning | Common cause | Resolution steps |
| --- | --- | --- | --- |
| AADSTS50076 | MFA required due to policy change | Conditional Access or security defaults now require MFA | Have the user complete MFA registration and retry. Review policy targeting. |
| AADSTS50079 | MFA registration required | User must register authentication methods | Direct the user to registration, verify allowed methods, and retry. |
| AADSTS53000 | Device is not compliant | Conditional Access requires compliant device state | Check Intune compliance, device registration, and targeting exclusions. |
| AADSTS53001 | Device not domain joined | Hybrid or joined-device requirement not met | Verify device join state and policy applicability. |
| AADSTS53003 | Blocked by Conditional Access | A policy explicitly blocked the sign-in | Inspect sign-in logs, identify the matched policy, and adjust targeting or controls. |
| AADSTS53004 | Proof-up blocked by policy | Authentication strength or method requirements not satisfied | Align the permitted methods with policy expectations and register required methods. |
| AADSTS50131 | Conditional Access failed | Claims challenge or device state unmet | Follow the claims challenge, use a supported client, and check device state. |
| AADSTS50158 | External security challenge not satisfied | Additional verification from federated or security service required | Review federation and MFA provider settings, then retry through the supported path. |
| AADSTS90072 | User not passed MFA challenge | MFA attempt failed or was interrupted | Retry MFA, verify network reachability for the method, and inspect method policy. |

### Client and app registration errors

| Error code | Typical meaning | Common cause | Resolution steps |
| --- | --- | --- | --- |
| AADSTS700016 | Application not found | Wrong client ID or app not provisioned in tenant | Validate `$APP_ID`, tenant context, and service principal existence. |
| AADSTS7000215 | Invalid client secret | Expired, incorrect, or wrong secret submitted | Rotate the secret, update the calling workload, and prefer certificates or managed identities when possible. |
| AADSTS7000222 | Invalid client credentials | Certificate or secret credential invalid | Confirm credential thumbprint, validity period, and tenant app mapping. |
| AADSTS700025 | Invalid client assertion | JWT client assertion malformed or signed incorrectly | Check issuer, audience, expiration, and signing certificate details. |
| AADSTS700027 | Client assertion expired or invalid signature | Clock skew or wrong signing material | Correct time sync, regenerate the assertion, and verify the certificate chain. |
| AADSTS700054 | Response type not enabled | Requested token type is disabled on the app | Enable the required platform setting, such as ID token issuance. |
| AADSTS70011 | Invalid scope | Scope string malformed or unavailable for resource | Request resource-specific delegated scopes exactly as exposed by the API. |
| AADSTS70043 | Refresh token invalid due to sign-in frequency checks | Conditional Access or revocation invalidated the session | Reauthenticate interactively and review token lifetime or sign-in frequency policies. |
| AADSTS700082 | Refresh token expired from inactivity | Long-lived session went unused too long | Prompt sign-in again and consider workload-specific token handling improvements. |
| AADSTS900144 | Request body missing required parameter | Malformed OAuth or OpenID Connect request | Rebuild the request with all required parameters and proper encoding. |

### Authorization and resource errors

| Error code | Typical meaning | Common cause | Resolution steps |
| --- | --- | --- | --- |
| AADSTS650052 | App needs access to a service not subscribed in tenant | Resource unavailable in the current tenant | Verify service licensing, tenant availability, and resource principal. |
| AADSTS650057 | Invalid resource | Requested API or resource identifier incorrect | Use the proper application ID URI or scope for the target API. |
| AADSTS65005 | Client requested more privileges than granted | Permission mismatch or stale consent | Compare requested scopes to granted scopes and re-consent as needed. |
| AADSTS50105 | User not assigned to application | Assignment required but user or group not assigned | Assign the user or group to the enterprise application or disable assignment requirement if appropriate. |
| AADSTS50107 | Requested federation realm object does not exist | Domain federation configuration missing | Verify domain federation state and repair realm configuration. |
| AADSTS501461 | AcceptMappedClaims not enabled | Token customization attempted without required app setting | Enable mapped claims support only when justified and understood. |
| AADSTS90002 | Tenant not found | Invalid tenant ID or deleted tenant | Validate `$TENANT_ID` and tenant state. |
| AADSTS9002331 | Native broker flow blocked | Client or platform does not satisfy broker requirements | Update the client, broker, or platform settings used for authentication. |

### Federation and external identity errors

| Error code | Typical meaning | Common cause | Resolution steps |
| --- | --- | --- | --- |
| AADSTS20012 | WS-Fed message invalid | Federation request malformed | Check relying party configuration and federation metadata. |
| AADSTS80001 | On-premises auth agent unavailable | Pass-through Authentication agent issue | Restore agent health, network access, and service connectivity. |
| AADSTS80002 | Password validation request timed out | PTA or federated infrastructure timeout | Review connectors, proxies, and agent performance. |
| AADSTS900382 | Confidential client not allowed in cross-cloud request | Cloud boundary mismatch | Ensure client and authority belong to the same cloud environment. |
| AADSTS901002 | Redirect URI parameter missing or invalid | App or request misconfiguration | Supply a valid redirect URI registered for the client. |

### Fast triage data points

| What to capture | Why it matters | Where to find it |
| --- | --- | --- |
| Correlation ID | Lets Microsoft trace the failed transaction | Error page, app logs, sign-in logs |
| Timestamp | Required for precise backend investigation | Error page, logs, SIEM |
| Tenant ID | Confirms authority and directory context | Token claims, portal, app configuration |
| Application ID | Identifies the affected client | App registration, logs, request parameters |
| Conditional Access policy result | Shows if policy blocked or challenged the sign-in | Sign-in logs in Entra admin center or Microsoft Graph |
| User ID or UPN | Links failure to account state and risk events | Sign-in logs, app logs |

## Usage Notes

- Start with sign-in logs before changing policy or app settings; they usually reveal the actual failing control.
- Capture correlation ID, trace ID, and timestamp for every production authentication failure.
- Many AADSTS errors are symptoms, not root causes; tenant targeting, consent state, and Conditional Access often drive the final code.
- If the issue involves Microsoft Graph, inspect both the OAuth failure and the API response body for layered errors.
- Reproduce with a minimal request to isolate whether the problem is the app, the identity, the policy, or the resource.

## See Also

- [Graph API quick reference](./graph-api-quick-reference.md)
- [Platform limits](./platform-limits.md)
- https://learn.microsoft.com/entra/identity-platform/reference-error-codes

## Sources

- https://learn.microsoft.com/entra/identity-platform/reference-error-codes
- https://learn.microsoft.com/entra/identity/conditional-access/troubleshoot-conditional-access
- https://learn.microsoft.com/entra/identity/authentication/howto-mfa-nps-extension-errors
- https://learn.microsoft.com/graph/errors
- https://learn.microsoft.com/entra/identity/monitoring-health/concept-sign-ins
