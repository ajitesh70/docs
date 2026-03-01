# Software Configuration – Ansible Role Documentation

---

| Author | Created On | Version | Last Updated By | Reviewer L0 | Reviewer L1 | Reviewer L2 |
|--------|------------|---------|-----------------|-------------|-------------|-------------|
| Ajitesh Singh | 05-01-2026 | v1 | Ajitesh Singh | Priyanshu | Faisal | Mahesh |

---

## Table of Contents

- [Introduction](#introduction)
- [What is an Ansible Role?](#what-is-an-ansible-role)
- [Why Ansible Roles?](#why-ansible-roles)
- [Workflow](#workflow)
- [Different Tools](#different-tools)
- [Comparison of Tools](#comparison-of-tools)
- [Advantages](#advantages)
- [Best Practices](#best-practices)
- [Recommendation & Conclusion](#recommendation--conclusion)
- [Contact Information](#contact-information)
- [References](#references)

---

## Introduction

Managing software configuration across multiple servers manually is error-prone, inconsistent, and difficult to scale. As infrastructure grows, teams need a reliable and repeatable way to install, configure, and maintain software across environments.

**Ansible** is an open-source automation tool that addresses this through agentless, YAML-based automation. **Ansible Roles** extend this by providing a structured, modular way to organize automation logic — making configurations reusable, testable, and shareable across teams and projects.

---

## What is an Ansible Role?

An Ansible Role is a standardized unit of automation that packages related tasks, variables, templates, handlers, and files into a defined directory structure. Rather than writing all automation logic in a single playbook, roles allow complex configurations to be broken into modular, self-contained components.

A role is initialized using:

```bash
ansible-galaxy init <role_name>
```

This generates the standard structure:

```
<role_name>/
├── defaults/       # Default variable values
├── tasks/          # Core automation logic
├── handlers/       # Event-driven actions (e.g. restart service)
├── templates/      # Jinja2 configuration file templates
├── vars/           # Higher-priority variable definitions
├── files/          # Static files to be copied to target hosts
└── meta/           # Role metadata and dependencies
```

Each directory has a specific purpose, and Ansible automatically loads them when the role is invoked from a playbook.

---

## Why Ansible Roles?

**Flat playbooks do not scale.** A single playbook managing Java, PostgreSQL, SonarQube, and systemd becomes long, hard to read, and difficult to maintain. Roles solve this by separating concerns into focused, independent units.

**Reusability.** A role written once can be applied to any number of hosts or projects without rewriting logic. For example, a PostgreSQL role can be reused across multiple applications.

**Consistency.** Every server configured by the same role receives identical configuration. There is no drift between environments caused by manual differences.

**Collaboration.** Roles can be versioned, shared via Ansible Galaxy, and reviewed like code — enabling teams to build on each other's work rather than duplicating effort.

---

## Workflow

<img width="410" height="741" alt="Screenshot 2026-03-01 191245" src="https://github.com/user-attachments/assets/53d70e8f-060b-4794-909c-4f89e6ac480c" />


---

## Different Tools

### Chef

Ruby-based configuration management tool using a client-server model. Configuration is written as "recipes" and "cookbooks". Requires a Chef server and agent installed on each managed node.

**Best for:** Large enterprises with complex configuration needs and existing Ruby expertise.

### Puppet

Declarative configuration management using its own DSL (Puppet language). Uses a master-agent architecture. Strong ecosystem for compliance and reporting.

**Best for:** Large-scale infrastructure with strict compliance and audit requirements.

### SaltStack

Python-based automation platform with both agent (minion) and agentless modes. Supports event-driven automation and real-time execution.

**Best for:** Large, dynamic infrastructures requiring high-speed execution and event-driven automation.

---

## Comparison of Tools

| Feature | Ansible | Chef | Puppet | SaltStack |
|---|---|---|---|---|
| Language | YAML | Ruby (DSL) | Puppet DSL | YAML / Python |
| Agent Required | No | Yes | Yes | Optional |
| Learning Curve | Low | High | Medium | Medium |
| Configuration Style | Procedural | Procedural | Declarative | Both |
| Reusable Modules | Roles | Cookbooks | Modules | Formulas |
| Community | Large | Large | Large | Medium |
| Best For | Config Mgmt | Enterprise | Compliance | Dynamic Infra |

Ansible is the most accessible starting point for teams adopting configuration management, particularly for software setup tasks like deploying SonarQube, configuring databases, and managing services.

---

## Advantages

- **Agentless** — No software needs to be installed on target hosts; Ansible connects via SSH.
- **Simple syntax** — YAML-based playbooks and roles are readable by developers and operations teams alike.
- **Idempotent** — Running the same role multiple times produces the same result without unintended side effects.
- **Modular** — Roles separate concerns cleanly, making large automation projects manageable.
- **Reusable** — A role written for one project can be applied across multiple environments and teams.
- **Version controlled** — Roles live in Git, giving full history, review, and rollback capability.
- **Extensible** — Ansible Galaxy provides thousands of community roles for common software stacks.

---

## Best Practices

- **Use roles for all non-trivial automation** — Avoid writing long flat playbooks; structure everything as roles from the start.
- **Store secrets in Ansible Vault** — Never commit plaintext passwords or tokens to version control.
- **Use `defaults/` for variables** — Place variables in `defaults/main.yml` so they can be easily overridden by consumers of the role.
- **Write idempotent tasks** — Every task should be safe to run multiple times. Use `state: present` and conditional checks to avoid unintended changes.
- **Use handlers for service restarts** — Never restart services directly in tasks; use handlers triggered by `notify` so restarts only happen when needed.
- **Tag your tasks** — Use Ansible tags to allow selective execution of specific parts of a role during troubleshooting or partial deployments.
- **Test roles before use** — Use Molecule to write and run automated tests for roles before deploying to production.
- **Pin versions** — Always specify exact versions for packages and application binaries to ensure reproducibility across environments.

---

## Recommendation & Conclusion

### Recommendation

For teams deploying and managing applications like SonarQube on cloud infrastructure, the recommended approach is:

- **Ansible** for all software configuration and service management
- **Ansible Roles** for modular, reusable automation logic
- **Ansible Vault** for secrets management
- **Ansible Galaxy** for leveraging community-maintained roles
- **Terraform** in combination with Ansible — Terraform to provision EC2 instances, Ansible to configure software on them

### Conclusion

Ansible Roles provide a clean, scalable approach to software configuration management. By enforcing a standard structure, they make automation more readable, testable, and maintainable. For a use case like deploying SonarQube with PostgreSQL and systemd on EC2, a role-based approach delivers a fully automated, repeatable deployment that can be applied to any environment with a single command. As infrastructure grows, the investment in well-structured roles pays compounding returns in reduced manual effort, consistent environments, and faster onboarding.

---

## Contact Information

| Name | Email |
|------|-------|
| Ajitesh Singh | ajitesh.singh.snaatak@mygurukulam.co |

---

## References

| Title | Link |
|---|---|
| Ansible Official Documentation | https://docs.ansible.com/ |
| Ansible Roles Guide | https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse_roles.html |
| Ansible Galaxy | https://galaxy.ansible.com/ |
| Ansible Vault | https://docs.ansible.com/ansible/latest/vault_guide/index.html |
| Molecule – Role Testing | https://molecule.readthedocs.io/en/latest/ |
