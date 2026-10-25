# ðŸ›¡ï¸ Enterprise DevSecOps Pipeline Architecture & Quality Gates

> **Author:** Rahul Goyal â€” Senior DevOps & Cloud Engineer (14+ Years)  
> **Standard:** ISO/IEC 27001 & CIS Azure Foundations Benchmark

---

## 1. Zero-Trust Pipeline Topology

```
+--------------------------------------------------------------------------------------------------+
|                                    AZURE DEVOPS MULTI-STAGE PIPELINE                             |
+--------------------------------------------------------------------------------------------------+
       |
       v
 [ STAGE 1: VALIDATE ]
   â”œâ”€â”€ terraform fmt -check (Style consistency)
   â”œâ”€â”€ terraform validate (Syntax & provider graph)
   â””â”€â”€ tflint (Deep linting & deprecation checks)
       |
       v
 [ STAGE 2: SECURITY SCAN (SHIFT-LEFT) ]
   â”œâ”€â”€ checkov (CIS Azure compliance scanning)
   â”œâ”€â”€ tfsec (Terraform static vulnerability analysis)
   â””â”€â”€ gitleaks (Zero credential leak enforcement)
       |
       v
 [ STAGE 3: COST GOVERNANCE (FINOPS GATE) ]
   â”œâ”€â”€ infracost breakdown (Diff analysis vs baseline)
   â””â”€â”€ Automated pull request cost comment
       |
       v
 [ STAGE 4: MANUAL APPROVAL GATE ]
   â”œâ”€â”€ Enforced Code Owners Review (Senior DevOps Lead & Security Lead)
   â””â”€â”€ Pre-deployment sign-off
       |
       v
 [ STAGE 5: TERRAFORM APPLY (OIDC FEDERATED) ]
   â”œâ”€â”€ Execution of exact, tamper-proof plan artifact
   â””â”€â”€ Automated Slack/Teams deployment notifications
```

---

## 2. Key Non-Negotiables
1. **Never apply code directly**: Pipelines apply binary plan artifacts generated in the plan stage, preventing drift between PR review and apply.
2. **Workload Identity Federation**: Static Azure Service Principal secrets are prohibited; all authentication uses short-lived tokens via OIDC.
3. **Automated Rollback Protocol**: Every deployment generates a backward-compatible restore state artifact prior to apply.

---
*Authored by [Rahul Goyal](https://github.com/rahulgoyaldevops)*
