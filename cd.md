# Ansible Playbook – Continuous Deployment (CD) Workflow

| Author | Created On | Version | Last Updated By | Reviewer L0 | Reviewer L1 | Reviewer L2 |
|--------|------------|---------|------------------|-------------|-------------|-------------|
| Ajitesh Singh | 21-01-2026 | v1.0 | Ajitesh Singh | Priyanshu | Faisal | Mahesh |

---
# Table of Contents

- [Overview](#overview)
- [Understanding Continuous Deployment](#understanding-continuous-deployment)
- [Core Components](#core-components)
- [Ansible CD Workflow Lifecycle](#ansible-cd-workflow-lifecycle)
- [Operational Best Practices](#operational-best-practices)
- [Contact Information](#contact-information)
- [References](#references)

---

## Overview

This document explains how Ansible Playbooks are used within a Continuous Deployment (CD) workflow to automate configuration and infrastructure changes. The workflow ensures that once code is approved and merged, deployments are executed automatically, safely, and consistently across target environments.


---

## Understanding Continuous Deployment

Continuous Deployment is a DevOps practice where approved code changes are **automatically deployed** to target environments without manual execution.
In an **Ansible CD workflow**, this means:
- Infrastructure and configuration changes are version-controlled
- Deployment is triggered after successful merge
- Playbooks enforce a consistent system state
- Rollbacks and audit trails are simplified

---

## Core Components

| Component | Description |
|---------|-------------|
| Playbook Repository | Stores Ansible playbooks, roles, and variables |
| Inventory Management | Static or dynamic host definitions |
| Secret Handling | Ansible Vault, CI secrets, or cloud secret managers |
| Approval Process | Pull Request review before merge |
| Deployment Trigger | Pipeline execution after merge to main/release branch |
| Execution Node | Ansible control node running playbooks |

---

## Ansible CD Workflow Lifecycle

### Step-by-Step Execution Flow

**1. Code Update**  
Developers modify Ansible playbooks, roles, or variables and push changes to the version control repository.

**2. Pull Request Validation**  
A Pull Request is raised and reviewed for:
- Syntax correctness
- Idempotency
- Security best practices
- Logic accuracy

**3. Approved Merge**  
After approval, changes are merged into the protected branch (`main` or `release`).

**4. Deployment Trigger**  
The merge event activates the CI/CD pipeline responsible for deployment.

**5. Secrets & Inventory Initialization**  
Sensitive information is decrypted securely, and inventory data is loaded for target hosts.

**6. Playbook Execution**  
The Ansible controller applies configurations to remote nodes:
```bash
ansible-playbook -i inventory/hosts site.yml

```
<img width="1087" height="680" alt="image" src="https://github.com/user-attachments/assets/af9084aa-9314-4d57-9c54-62666fe46942" />


---

## Operational Best Practices

- Never store sensitive information such as passwords, SSH keys, or tokens directly in playbooks or repositories.
- Always use **Ansible Vault** or CI/CD secret managers to handle confidential data securely.
- Keep playbooks **idempotent** so that running them multiple times does not cause unexpected changes.
- Break large playbooks into **roles** to improve reusability and maintainability.
- Validate playbooks before deployment using check mode:
  ```bash
  ansible-playbook site.yml --check
  ```
  Use tags to execute only specific parts of a playbook when required:
  ```
  ansible-playbook site.yml --tags "app"
  ```
---
## Contact Information

| Name | Email |
|------|-------|
| Ajitesh Singh | ajitesh.singh.snaatak@mygurukulam.co |

---

## References

| Link | Description |
|------|-------------|
| https://docs.ansible.com | Ansible Documentation |
| https://docs.ansible.com/ansible/latest/playbooks.html | Playbook Guide |
| https://www.redhat.com/en/topics/automation/what-is-ansible | Ansible Overview |

---
