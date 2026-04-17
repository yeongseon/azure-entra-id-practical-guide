# AGENTS.md

Guidance for AI agents working in this repository.

## Project Overview

**Azure Entra ID Practical Guide** — a unified documentation hub for implementing, operating, and troubleshooting Microsoft Entra ID (formerly Azure Active Directory), including authentication, conditional access, governance, and hybrid identity.

- **Live site**: <https://yeongseon.github.io/azure-entra-id-practical-guide/>
- **Repository**: <https://github.com/yeongseon/azure-entra-id-practical-guide>

## Repository Structure

```text
.
├── .github/
│   └── workflows/              # GitHub Pages deployment
├── docs/
│   ├── assets/
│   │   └── images/             # Static site assets
│   ├── best-practices/         # Production patterns and anti-patterns (7 pages)
│   ├── contributing/           # Contributing guide (1 page)
│   ├── javascripts/            # Mermaid zoom JS
│   ├── main.html               # MkDocs Material template override
│   ├── operations/             # Day-2 operational execution (8 pages)
│   ├── platform/               # Architecture and design decisions (9 pages)
│   ├── reference/              # CLI cheatsheet, Graph API, limits, error codes (4 pages)
│   ├── scenarios/              # Per-scenario identity configurations (15 pages)
│   │   ├── app-registration/   # App registration + 2 deep dives
│   │   ├── conditional-access/ # Conditional access + 2 deep dives
│   │   ├── hybrid-identity/    # Hybrid identity + 2 deep dives
│   │   ├── b2b-collaboration/  # B2B collaboration + 2 deep dives
│   │   └── governance/         # Governance + 2 deep dives
│   ├── start-here/             # Overview, learning paths, repository map (3 pages)
│   ├── stylesheets/            # Custom CSS
│   └── troubleshooting/        # Decision tree, playbooks, first 10 minutes (16 pages)
│       ├── playbooks/          # 7 playbooks with investigation steps
│       └── first-10-minutes/   # 4 quick diagnosis cards
└── mkdocs.yml                  # MkDocs Material configuration (8-tab nav)
```

## Content Categories

The documentation is organized by intent and lifecycle stage:

| Section | Purpose | Page Count |
|---|---|---|
| **Start Here** | Entry points, learning paths, repository map | 3 |
| **Platform** | Architecture, design decisions — WHAT and HOW Entra ID works | 9 |
| **Best Practices** | Production patterns — HOW to use Entra ID well | 7 |
| **Scenarios** | Per-scenario identity setup and configuration | 15 |
| **Operations** | Day-2 execution — HOW to run identity in production | 8 |
| **Troubleshooting** | Diagnosis and resolution — hypothesis-driven playbooks | 16 |
| **Reference** | Quick lookup — CLI, Graph API, platform limits, error codes | 4 |

!!! info "Platform vs Best Practices vs Operations"
    - **Platform** = Understand Entra ID architecture, tenants, authentication, and tokens.
    - **Best Practices** = Apply practical patterns for conditional access, RBAC, and identity protection.
    - **Operations** = Execute day-2 tasks like user lifecycle, consent management, and log analysis.

## Documentation Conventions

### File Naming

- All files: `topic-name.md` (kebab-case)
- Index files: `index.md` in each directory

### CLI Command Style

```bash
# ALWAYS use long flags for readability
az ad user create \
    --display-name $DISPLAY_NAME \
    --user-principal-name $UPN \
    --password $PASSWORD

# NEVER use short flags in documentation
az ad user create -n $DISPLAY_NAME  # Don't do this
```

### Variable Naming Convention

| Variable | Description | Example |
|----------|-------------|---------|
| `$TENANT_ID` | Entra ID tenant identifier | `<tenant-id>` |
| `$APP_ID` | Application (client) ID | `<app-id>` |
| `$OBJECT_ID` | Object ID of any Entra ID object | `<object-id>` |
| `$SP_ID` | Service principal object ID | `<sp-object-id>` |
| `$DISPLAY_NAME` | Display name for users/groups/apps | `demo-app-001` |
| `$UPN` | User principal name | `user@example.com` |
| `$RG` | Resource group name | `rg-identity-demo` |
| `$LOCATION` | Azure region | `koreacentral` |
| `$SUBSCRIPTION_ID` | Subscription identifier placeholder | `<subscription-id>` |
| `$CLIENT_SECRET` | Application client secret placeholder | `<client-secret>` |
| `$REDIRECT_URI` | Application redirect URI | `https://example.com/callback` |

### PII Removal (Quality Gate)

**CRITICAL**: All CLI output examples MUST have PII removed.

**Must mask (real Azure identifiers):**

- Subscription IDs: `<subscription-id>`
- Tenant IDs: `<tenant-id>`
- Object IDs: `<object-id>`
- Application IDs: `<app-id>`
- Resource IDs containing real subscription/tenant
- Emails: Remove or mask as `user@example.com`
- Secrets/Tokens: NEVER include
- Connection strings: Replace with `<connection-string>`

**OK to keep (synthetic example values):**

- Demo correlation IDs: `a1b2c3d4-e5f6-7890-abcd-ef1234567890`
- Example request IDs in logs
- Placeholder domains: `example.com`, `contoso.com`
- Sample resource names used consistently in docs

### Admonition Indentation Rule

For MkDocs admonitions (`!!!` / `???`), every line in the body must be indented by **4 spaces**.

```markdown
!!! warning "Important"
    This line is correctly indented.

    - List item also inside
```

### Mermaid Diagrams

All architectural diagrams use Mermaid. Every documentation page should include at least one diagram. Test with `mkdocs build --strict`.

#### Diagram Orientation Rule

- **Sequential flows with 5+ nodes**: Use `flowchart TD` (top-down) to prevent horizontal overflow.
- **Short diagrams with fewer than 5 nodes**: `flowchart LR` (left-right) is acceptable.
- **Layered architecture diagrams** (e.g., identity layers, trust chains): Always use `flowchart TD`.

Common diagram types for Entra ID:

- Authentication flow diagrams (user → app → Entra ID → token)
- Conditional access policy evaluation flows
- Hybrid identity sync topology
- Tenant and subscription relationships
- App registration and service principal relationships

### Nested List Indentation

All nested list items MUST use **4-space indent** (Python-Markdown standard).

### Tail Section Naming

Every document ends with these tail sections (in this order):

| Section | Purpose | Content |
|---|---|---|
| `## See Also` | Internal cross-links within this repository | Links to other pages in this guide |
| `## Sources` | External authoritative references | Links to Microsoft Learn (primary) |

- `## See Also` is required on every page.
- `## Sources` is required on every page and must cite Microsoft Learn references.
- Order is always `## See Also` → `## Sources` (never reversed).
- All content must be based on Microsoft Learn with cited sources.

### Canonical Document Templates

Every document follows one of 5 templates based on its section. Do not invent new structures.

#### Platform docs

```text
# Title
Brief introduction (1-2 sentences)

## Architecture Overview
[Mermaid diagram showing component relationships]
[Detailed explanation of architecture]

## Core Concepts
### Concept 1
[Explanation with CLI examples]

### Concept 2
[Explanation with CLI examples]

## Data Flow
[How data moves through the system]

## Integration Points
[How this component connects to others]

## Configuration Options
[Key settings with CLI examples]

## Pricing Considerations
[Cost factors and optimization tips]

## Limitations and Quotas
[Platform limits to be aware of]

## Advanced Topics (optional)
## See Also
## Sources
```

**Platform page requirements:**
- 400-600 lines
- Minimum 1 Mermaid diagram
- Minimum 3 CLI examples with output
- Must explain WHAT, HOW, and WHY

#### Best Practices docs

```text
# Title
Brief introduction (1-2 sentences)

## Why This Matters
[Business impact and risk context]

## Prerequisites
[Required resources and permissions]

## Recommended Practices
### Practice 1: [Name]
**Why**: [Rationale]
**How**: [CLI commands with output]
**Validation**: [How to verify]

### Practice 2: [Name]
[Same structure]

## Common Mistakes / Anti-Patterns
### Anti-Pattern 1: [Name]
**What happens**: [Symptom]
**Why it's wrong**: [Impact]
**Correct approach**: [Fix with CLI]

## Validation Checklist
- [ ] Item 1
- [ ] Item 2

## Cost Impact
[How these practices affect billing]

## See Also
## Sources
```

#### Operations docs

```text
# Title
Brief introduction (1-2 sentences)

## Prerequisites
[Required resources, permissions, tools]

## When to Use
[Scenarios that trigger this operation]

## Procedure
### Step 1: [Action]
[CLI command with --long-flags]
[Expected output]
[Explanation]

### Step 2: [Action]
[Same structure]

## Verification
[How to confirm the operation succeeded]

## Rollback / Troubleshooting
[What to do if something goes wrong]

## Automation
[Scripting and scheduling options]

## See Also
## Sources
```

#### Playbooks

```text
# Title

## 1. Summary
## 2. Common Misreadings (table)
## 3. Competing Hypotheses (table)
## 4. What to Check First
## 5. Evidence to Collect
### 5.1 Sign-in Log Investigation
### 5.2 CLI / Graph API Investigation
## 6. Validation and Disproof by Hypothesis
## 7. Likely Root Cause Patterns (table)
## 8. Immediate Mitigations
## 9. Prevention (optional)
## See Also
## Sources
```

**Playbook requirements:**
- 380-480 lines
- ALL 9 numbered sections required
- Minimum 2 Graph API/CLI investigation commands
- Tables for hypotheses and evidence interpretation

#### Reference docs

```text
# Title
Brief introduction (1-2 sentences)

## Command Reference / Data Tables
### Category 1
### Category 2

## Usage Notes
## See Also
## Sources
```

## Content Source Requirements

### 1. MSLearn-First Policy

All content MUST be traceable to official Microsoft Learn documentation:

- Platform content: MUST have direct MSLearn source URLs
- Architecture diagrams: MUST reference official Microsoft documentation
- Troubleshooting playbooks: MAY synthesize MSLearn content with clear attribution
- Self-generated content: MUST have justification explaining the source basis

### 2. Source Types

| Type | Description | Allowed? |
|---|---|---|
| `mslearn` | Directly from Microsoft Learn | Required for platform content |
| `mslearn-adapted` | MSLearn content adapted for this guide | Allowed with source URL |
| `self-generated` | Original content for this guide | Requires justification |
| `community` | From community sources | Not for core content |
| `unknown` | Source not documented | Must be validated |

### 3. Diagram Source Documentation

Every Mermaid diagram MUST have source metadata in frontmatter:

```yaml
content_sources:
  diagrams:
    - id: architecture-overview
      type: flowchart
      source: mslearn
      mslearn_url: https://learn.microsoft.com/en-us/entra/fundamentals/
    - id: auth-flow
      type: flowchart
      source: self-generated
      justification: "Synthesized from Microsoft Learn articles"
      based_on:
        - https://learn.microsoft.com/en-us/entra/identity-platform/
```

### Text Content Validation

Every non-tutorial document should include a `content_validation` block in frontmatter.

```yaml
---
content_sources:
  - type: mslearn-adapted
    url: https://learn.microsoft.com/en-us/entra/...
content_validation:
  status: verified
  last_reviewed: 2026-04-17
  reviewer: agent
  core_claims:
    - claim: "{example claim}"
      source: https://learn.microsoft.com/en-us/entra/...
      verified: true
---
```

## Quality Gates

### Line Count Requirements

| Section | Minimum Lines | Target Lines |
|---|---|---|
| Platform | 300 | 400-600 |
| Best Practices | 200 | 250-350 |
| Operations | 200 | 250-350 |
| Playbooks | 350 | 380-480 |
| Reference | 100 | 150-300 |

### Required Elements Checklist

Every page must have:

- [ ] At least one Mermaid diagram (except Reference pages)
- [ ] CLI examples with `--long-flags` only
- [ ] `## See Also` section with internal links
- [ ] `## Sources` section with Microsoft Learn URLs
- [ ] 4-space indentation for nested lists
- [ ] No short flags (`-g`, `-n`, etc.)
- [ ] No PII in example output

### Microsoft Learn Source Requirement

**All content must be based on Microsoft Learn documentation.**

- Primary source: `https://learn.microsoft.com/en-us/entra/`
- Secondary sources: `https://learn.microsoft.com/en-us/azure/` (related services)
- Every page must cite at least one Microsoft Learn URL in `## Sources`

## Build & Preview

```bash
pip install mkdocs-material mkdocs-minify-plugin
mkdocs build --strict
mkdocs serve
```

## Git Commit Style

```text
type: short description
```

Allowed types: `feat`, `fix`, `docs`, `chore`, `refactor`

## Microsoft Entra ID Domain Knowledge

### Key Services Covered

| Service | Purpose | Key APIs |
|---|---|---|
| Microsoft Entra ID | Cloud identity and access management | Microsoft Graph `/users`, `/groups` |
| Conditional Access | Policy-based access control | Microsoft Graph `/identity/conditionalAccess/policies` |
| Identity Protection | Risk-based identity security | Microsoft Graph `/identityProtection/riskDetections` |
| Privileged Identity Management | Just-in-time privileged access | Microsoft Graph `/roleManagement` |
| Entitlement Management | Access lifecycle governance | Microsoft Graph `/identityGovernance/entitlementManagement` |
| External Identities | B2B and B2C collaboration | Microsoft Graph `/invitations` |
| Entra Connect | Hybrid identity synchronization | On-premises agent |

### Common CLI Patterns

```bash
# List users
az ad user list --query "[].{UPN:userPrincipalName, ID:id}" --output table

# Create app registration
az ad app create --display-name $DISPLAY_NAME --sign-in-audience AzureADMyOrg

# List service principals
az ad sp list --display-name $DISPLAY_NAME --output table

# Query sign-in logs via Graph API
az rest --method GET \
    --url "https://graph.microsoft.com/v1.0/auditLogs/signIns" \
    --headers "Content-Type=application/json"
```

### CLI Command Groups

| Command Group | Purpose |
|---|---|
| `az ad user` | User management |
| `az ad group` | Group management |
| `az ad app` | Application registration |
| `az ad sp` | Service principal management |
| `az role assignment` | RBAC role assignments |
| `az rest` | Direct Microsoft Graph API calls |

## Related Projects

| Repository | Description |
|---|---|
| [azure-virtual-machine-practical-guide](https://github.com/yeongseon/azure-virtual-machine-practical-guide) | Azure Virtual Machines practical guide |
| [azure-networking-practical-guide](https://github.com/yeongseon/azure-networking-practical-guide) | Azure Networking practical guide |
| [azure-storage-practical-guide](https://github.com/yeongseon/azure-storage-practical-guide) | Azure Storage practical guide |
| [azure-app-service-practical-guide](https://github.com/yeongseon/azure-app-service-practical-guide) | Azure App Service practical guide |
| [azure-functions-practical-guide](https://github.com/yeongseon/azure-functions-practical-guide) | Azure Functions practical guide |
| [azure-container-apps-practical-guide](https://github.com/yeongseon/azure-container-apps-practical-guide) | Azure Container Apps practical guide |
| [azure-communication-services-practical-guide](https://github.com/yeongseon/azure-communication-services-practical-guide) | Azure Communication Services practical guide |
| [azure-kubernetes-service-practical-guide](https://github.com/yeongseon/azure-kubernetes-service-practical-guide) | Azure Kubernetes Service (AKS) practical guide |
| [azure-architecture-practical-guide](https://github.com/yeongseon/azure-architecture-practical-guide) | Azure Architecture practical guide |
| [azure-monitoring-practical-guide](https://github.com/yeongseon/azure-monitoring-practical-guide) | Azure Monitoring practical guide |
