---
name: Infrastructure-as-Code Security Rules
description:
category: security
paths:
  - "**/*.tf"
  - "**/*.tfvars"
  - "**/terraform/**"
  - "**/cloudformation/**"
  - "**/*.template.json"
  - "**/*.template.yaml"
  - "**/pulumi/**"
  - "**/Pulumi.yaml"
  - "**/Pulumi.*.yaml"
---

## Infrastructure as Code

- All infrastructure changes go through version control.
- Use `terraform plan` (or equivalent) to preview changes before applying.
- Store state remotely with locking.
- Never commit secrets in IaC files. Use variable references to secret managers.
- Tag all resources with owner, project, environment, and cost center.
