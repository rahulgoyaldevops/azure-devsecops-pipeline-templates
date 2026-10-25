# 🌿 Branching Strategy — Trunk-Based Development

## Overview

We follow a **trunk-based branching strategy** optimized for Infrastructure as Code (IaC) workflows.

## Rules

| Rule | Description |
|:--|:--|
| **Protected main branch** | No direct pushes; all changes via PR |
| **Short-lived feature branches** | Max 2 days; named `feature/`, `fix/`, `hotfix/` |
| **Mandatory PR reviews** | Minimum 2 reviewers; CODEOWNERS enforced |
| **No branch deletion protection** | Main branch cannot be deleted |
| **Auto-delete merged branches** | Keeps repo clean |
| **Required status checks** | All CI checks must pass before merge |

## Flow

```
main ─────────────────────────────────────────────────▶
       \                                    /
        feature/add-nsg ──── PR ──── merge
              │
              ├── terraform validate ✅
              ├── tflint ✅
              ├── checkov scan ✅
              ├── terraform plan ✅
              ├── infracost ✅
              └── 2 approvals ✅
```

## Naming Convention

- `feature/TICKET-123-add-firewall-rules`
- `fix/TICKET-456-correct-cidr-range`
- `hotfix/TICKET-789-urgent-nsg-fix`