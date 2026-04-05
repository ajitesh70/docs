# Terraform Static Infrastructure Documentation

---

| Author | Created On | Version | Last Updated By | Reviewer L0 | Reviewer L1 | Reviewer L2 |
|--------|------------|---------|-----------------|-------------|-------------|-------------|
| Ajitesh Singh | 05-04-2026 | v1 | Ajitesh Singh | Priyanshu | Faisal | Mahesh |

---

## Table of Contents

- [Introduction](#introduction)
- [What is Static Infrastructure?](#what-is-static-infrastructure)
- [Why Static Infrastructure?](#why-static-infrastructure)
- [Directory Structure](#directory-structure)
- [Resources Provisioned](#resources-provisioned)
- [Environment-Specific Values](#environment-specific-values)
- [Dependencies](#dependencies)
- [Best Practices](#best-practices)
- [Conclusion](#conclusion)
- [Contact Information](#contact-information)
- [References](#references)

---

## Introduction

Static infrastructure refers to foundational cloud resources that are provisioned once and shared across workloads. This document describes the purpose, structure, environment-specific values, and dependencies of the Terraform static module — enabling teams to understand and safely modify the codebase.

---

## What is Static Infrastructure?

Static infrastructure consists of long-lived resources that persist across deployments and are shared by multiple workloads.

| Category | Examples |
|---|---|
| Networking | VPC, Subnets, Internet Gateway, NAT Gateway, Route Tables |
| Security | Security Groups, NACLs |
| DNS | Route 53 Hosted Zones |

---

## Why Static Infrastructure?

| Reason | Details |
|---|---|
| Stability | Foundation layer must not change with every application release |
| Separation of concerns | Managed independently from application deployments |
| Reusability | Multiple workloads share the same VPC, subnets, and security groups |
| Safety | Prevents accidental destruction of shared resources during app updates |

---

## Directory Structure

```
terraform/
└── static/
    ├── main.tf              # Root module
    ├── variables.tf         # Input variable declarations
    ├── outputs.tf           # Values exported for other modules
    ├── terraform.tfvars     # Environment-specific values
    ├── provider.tf          # AWS provider configuration
    └── backend.tf           # Remote state — S3 + DynamoDB
```

---

## Resources Provisioned

| Resource | Details |
|---|---|
| VPC | CIDR `10.0.0.0/17`, region `eu-north-1` |
| Public Subnets | AZ 1a (`10.0.1.0/24`), AZ 1b (`10.0.4.0/24`) |
| Private Subnets | AZ 1a (`10.0.2.0/24`), AZ 1b (`10.0.5.0/24`) |
| Internet Gateway | Allows inbound/outbound internet traffic |
| NAT Gateway | Allows private EC2s to reach internet for updates |
| Public Route Table | `0.0.0.0/0 → IGW` |
| Private Route Table | `0.0.0.0/0 → NAT GW` |
| Security Groups | pub-sg, pvt-sg |
| NACL | Subnet-level traffic filtering |

---

## Environment-Specific Values

Set in `terraform.tfvars` per environment. No code changes needed between environments.

| Variable | Dev | Staging | Production |
|---|---|---|---|
| `env` | dev | staging | prod |
| `vpc_cidr` | 10.0.0.0/17 | 10.1.0.0/17 | 10.2.0.0/17 |
| `region` | eu-north-1 | eu-north-1 | eu-north-1 |

> Never commit sensitive values like IP addresses or secrets to version control.

---

## Dependencies

| Dependency | Details |
|---|---|
| Terraform | Version `>= 1.5.0` |
| AWS Provider | `hashicorp/aws ~> 5.0` |
| S3 Bucket | Must exist before `terraform init` — stores remote state |
| DynamoDB Table | Must exist before `terraform init` — handles state locking |

---

## Best Practices

| Practice | Details |
|---|---|
| Never destroy without review | VPC and subnets are shared — deletion affects all workloads |
| Always run `terraform plan` first | Review changes before applying to static resources |
| Use remote state | S3 + DynamoDB locking prevents state conflicts |
| Tag all resources | Apply `env`, `project`, `owner` tags to every resource |
| Pin provider versions | Prevents unexpected behaviour from provider upgrades |

---

## Conclusion

The Terraform static module provides the foundational networking and security layer for the infrastructure. Separating static resources from dynamic workloads ensures teams can deploy applications safely without risking changes to shared components. Environment-specific values are parameterised through `terraform.tfvars`, making the module reusable across dev, staging, and production.

---

## Contact Information

| Name | Email |
|------|-------|
| Ajitesh Singh | ajitesh.singh.snaatak@mygurukulam.co |

---

## References

| Title | Link |
|---|---|
| Terraform AWS Provider | https://registry.terraform.io/providers/hashicorp/aws/latest/docs |
| Terraform Modules | https://developer.hashicorp.com/terraform/language/modules |
| AWS VPC Documentation | https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html |
