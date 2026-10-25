<div align="center">

# 🔒 Azure DevSecOps Pipeline Templates

[![Azure DevOps](https://img.shields.io/badge/Azure_DevOps-Pipelines-0078D7?style=for-the-badge&logo=azuredevops&logoColor=white)](https://azure.microsoft.com/en-us/products/devops)
[![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-CI%2FCD-2088FF?style=for-the-badge&logo=githubactions&logoColor=white)](https://github.com/features/actions)
[![Terraform](https://img.shields.io/badge/Terraform-IaC-623CE4?style=for-the-badge&logo=terraform&logoColor=white)](https://www.terraform.io/)
[![Security](https://img.shields.io/badge/Security-Shift_Left-c62828?style=for-the-badge&logo=snyk&logoColor=white)](https://snyk.io/)

**Battle-tested, reusable CI/CD pipeline templates for enterprise Azure infrastructure with security baked in at every stage.**

*Built from 14+ years of production delivery experience.*

</div>

---

## 🎯 Why This Exists

> Most teams treat CI/CD as an afterthought — a single YAML file that runs `terraform apply`.
> That breaks in production. This repo provides **enterprise-grade pipeline templates** with
> validation, security scanning, cost estimation, manual approvals, drift detection, and rollback.

## 🏗️ Pipeline Architecture

```
┌─────────────────────────── DevSecOps Pipeline ─────────────────────────────┐
│                                                                             │
│  ┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐ │
│  │ VALIDATE │──▶│  BUILD   │──▶│ SECURITY │──▶│ APPROVAL │──▶│  DEPLOY  │ │
│  │          │   │          │   │          │   │          │   │          │  │
│  │ tf fmt   │   │ tf plan  │   │ checkov  │   │ Manual   │   │ tf apply │  │
│  │ tf valid │   │ infracost│   │ tfsec    │   │ Code     │   │ Smoke    │  │
│  │ tflint   │   │ cost est │   │ gitleaks │   │ Owners   │   │ Tests    │  │
│  │          │   │          │   │ trivy    │   │ Review   │   │          │  │
│  └──────────┘   └──────────┘   └──────────┘   └──────────┘   └──────────┘ │
│                                                                     │       │
│                                                              ┌──────▼─────┐ │
│                                                              │  ROLLBACK  │ │
│                                                              │  Template  │ │
│                                                              └────────────┘ │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────────┐ │
│  │ 🔄 DRIFT DETECTION (Scheduled)                                        │ │
│  │ Runs terraform plan on schedule → Alerts on infrastructure drift      │ │
│  └─────────────────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────────────────┘
```

## 📁 Repository Structure

```
azure-devsecops-pipeline-templates/
│
├── azure-devops/                         # Azure DevOps YAML Pipelines
│   ├── templates/
│   │   ├── terraform-validate.yml        # Format check + validate + lint
│   │   ├── terraform-plan.yml            # Plan + cost estimation
│   │   ├── terraform-apply.yml           # Apply with approval gate
│   │   ├── terraform-destroy.yml         # Controlled teardown
│   │   ├── security-scan.yml             # Checkov + tfsec + gitleaks
│   │   └── drift-detection.yml           # Scheduled drift check
│   │
│   ├── pipelines/
│   │   ├── infra-ci.yml                  # Full CI pipeline (PR trigger)
│   │   ├── infra-cd.yml                  # Full CD pipeline (merge trigger)
│   │   └── drift-scheduled.yml           # Cron-based drift detection
│   │
│   └── variable-groups/
│       └── example-vars.md               # Variable group documentation
│
├── github-actions/                        # GitHub Actions Workflows
│   ├── terraform-ci.yml                  # CI on Pull Request
│   ├── terraform-cd.yml                  # CD on merge to main
│   ├── drift-detection.yml               # Scheduled drift check
│   └── pr-comment-plan.yml              # Post plan output to PR
│
├── scripts/
│   ├── validate.sh                       # Pre-flight validation
│   ├── cost-check.sh                     # Infracost threshold check
│   └── rollback.sh                       # Terraform state rollback
│
├── policies/
│   ├── checkov/                          # Custom Checkov policies
│   └── opa/                              # OPA/Rego policy rules
│
├── docs/
│   ├── BRANCHING_STRATEGY.md             # Trunk-based branching guide
│   ├── SECURITY_SCANNING.md              # Security tools configuration
│   └── COST_GOVERNANCE.md                # FinOps in pipelines
│
└── README.md
```

## 🛡️ Security Gates

| Stage | Tool | Purpose |
|:--|:--|:--|
| **Pre-commit** | `gitleaks` | Prevent secrets from entering the repo |
| **Lint** | `tflint` | Catch misconfigurations before plan |
| **Static Analysis** | `checkov` | CIS benchmark compliance checks |
| **Static Analysis** | `tfsec` | Terraform-specific security scanning |
| **Container Scan** | `trivy` | Container image vulnerability scanning |
| **Cost Guard** | `infracost` | Block deployments exceeding cost thresholds |
| **Policy Check** | `OPA/Rego` | Custom organizational policy enforcement |
| **Secret Scan** | `gitleaks` | CI-level secret detection |

## 🔄 Branching Strategy

```
main (protected)
  │
  ├── feature/add-nsg-rules ──── PR ──── Code Review ──── Merge
  │     (short-lived)            │
  │                        Auto-triggers:
  │                        ✅ terraform validate
  │                        ✅ tflint
  │                        ✅ checkov scan
  │                        ✅ terraform plan
  │                        ✅ infracost estimate
  │                        ✅ Plan output as PR comment
  │
  └── (merge to main) ──── Auto-triggers:
                            ✅ terraform apply
                            ✅ Smoke tests
                            ✅ Slack/Teams notification
```

**Rules:**
- ❌ No direct pushes to `main`
- ✅ PR mandatory with minimum 2 reviewers
- ✅ CODEOWNERS file for module-level ownership
- ✅ Feature branches are short-lived (< 2 days)
- ✅ Branch auto-delete after merge

## 📊 Drift Detection

```yaml
# Runs every 6 hours — alerts if infrastructure has drifted from Terraform state
schedule:
  - cron: '0 */6 * * *'

# If drift detected → creates GitHub Issue / Teams alert with plan diff
```

## 🚀 Quick Start

```bash
# 1. Copy templates to your repo
cp -r azure-devops/templates/ your-repo/.azure-pipelines/templates/

# 2. Reference in your pipeline
trigger:
  - main
stages:
  - template: templates/terraform-validate.yml
  - template: templates/security-scan.yml
  - template: templates/terraform-plan.yml
  - template: templates/terraform-apply.yml
    parameters:
      environment: 'production'
      approvers: 'platform-team'
```

---

<div align="center">

**Built by [Rahul Goyal](https://github.com/rahulgoyaldevops)** — Enterprise Cloud Architect | 14+ Years

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Rahul_Goyal-0A66C2?style=flat-square&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/rahulgoyaldevops/)

</div>