# Ansible Playbook – CI Documentation

---

| Author | Created On | Version | Last Updated By | Reviewer L0 | Reviewer L1 | Reviewer L2 |
|--------|------------|---------|-----------------|-------------|-------------|-------------|
| Ajitesh Singh | 05-01-2026 | v1 | Ajitesh Singh | Priyanshu | Faisal | Mahesh |

---

## Table of Contents

- [Introduction](#introduction)
- [What is CI for Ansible Playbooks?](#what-is-ci-for-ansible-playbooks)
- [Why CI is Important?](#why-ci-is-important)
- [How Ansible Playbook is Tested in CI?](#how-ansible-playbook-is-tested-in-ci)
  - [YAML Syntax Check](#yaml-syntax-check)
  - [Ansible Syntax Check](#ansible-syntax-check)
  - [Ansible Lint](#ansible-lint)
  - [Dry Run (Check Mode)](#dry-run-check-mode)
- [Conclusion](#conclusion)
- [Contact Information](#contact-information)
- [References](#references)

---

## Introduction

This document explains what CI (Continuous Integration) is for Ansible Playbooks, why it is important, and how playbooks are tested before deployment to ensure quality and prevent failures.

---

## What is CI for Ansible Playbooks?

CI is a process where the Ansible Playbook is automatically tested every time code is:

- Pushed to the repository
- A Pull Request is created or updated

CI ensures that the playbook is correct and follows best practices before it is used in real environments.

---

## Why CI is Important?

- **Finds mistakes before deployment** — Automatically detects errors early, before the playbook runs on real servers
- **Keeps playbook clean and correct** — Ensures proper YAML format and correct syntax
- **Improves code quality** — Checks for common mistakes and improves overall playbook standards
- **Follows best practices** — Linting tools ensure the playbook is written using recommended methods
- **Reduces manual checking** — Automatic checks save time and reduce human effort
- **Prevents production failures** — Fixing issues early avoids problems in live environments

---

## How Ansible Playbook is Tested in CI?

The CI pipeline runs the following checks automatically on every push or pull request.

### YAML Syntax Check

Ansible Playbooks are written in YAML. If the YAML format is wrong, the playbook will fail.

**Tool:** `yamllint`

```bash
yamllint playbook.yml
```

This checks:

- Indentation
- Spacing
- YAML structure errors

---

### Ansible Syntax Check

Validates whether the playbook structure is correct.

```bash
ansible-playbook --syntax-check playbook.yml
```

This ensures:

- No syntax mistakes
- Proper task structure
- Correct module usage format

---

### Ansible Lint

Checks best practices and common mistakes in the playbook.

```bash
ansible-lint playbook.yml
```

It checks:

- Use of latest module syntax
- Avoidance of deprecated modules
- Proper task naming
- Idempotency practices

---

### Dry Run (Check Mode)

Runs the playbook in check mode to preview what changes will happen without actually applying them.

```bash
ansible-playbook playbook.yml --check
```

This helps verify:

- What tasks will change
- Safe execution preview before real deployment

---

## Conclusion

Ansible Playbook CI ensures that the playbook is syntax correct, properly formatted, and follows best practices. By running automatic checks before deployment, CI helps identify issues early and prevents failures in real environments.

---

## Contact Information

| Name | Email |
|------|-------|
| Ajitesh Singh | ajitesh.singh.snaatak@mygurukulam.co |

---

## References

| Title | Link |
|---|---|
| Ansible Playbooks Introduction | https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_intro.html |
| ansible-playbook CLI (Syntax Check) | https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html |
| Ansible Lint Documentation | https://ansible-lint.readthedocs.io/ |
| YAML Lint Documentation | https://yamllint.readthedocs.io/ |
