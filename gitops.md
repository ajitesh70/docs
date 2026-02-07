# GitOps Strategy – Documentation

## Author
Ajitesh Singh

---

## Table of Contents
- Introduction
- What is GitOps
- Why GitOps
- Types of GitOps Workflows
- Comparison of GitOps Workflows
- Conclusion (Chosen GitOps Strategy)
- Contact Information
- References

---

## Introduction

GitOps is a modern DevOps operating model where **Git acts as the single source of truth** for application and infrastructure configuration.  
All changes are made through Git, reviewed via pull/merge requests, and automatically applied to environments.

This document defines the **GitOps strategies that will be implemented** for the platform.

---

## What is GitOps

GitOps is a practice that uses:
- Declarative configuration
- Git-based version control
- Automated synchronization

to manage infrastructure and application deployments in a reliable and auditable way.

---

## Why GitOps

GitOps is adopted to achieve:

- Consistent and repeatable deployments
- Full audit trail of changes
- Faster rollback and recovery
- Reduced manual intervention
- Improved security and compliance
- Better collaboration between Dev and Ops teams

---

## Types of GitOps Workflows

### 1. Push-based GitOps

- CI pipeline pushes changes to the target environment
- Deployment credentials are stored in CI system

**Examples:** Jenkins pushing manifests to Kubernetes

---

### 2. Pull-based GitOps

- Agent running inside the environment pulls changes from Git
- Environment continuously reconciles desired state

**Examples:** Argo CD, Flux CD

---

## Comparison of GitOps Workflows

| Aspect | Push-based GitOps | Pull-based GitOps |
|------|------------------|------------------|
| Security | Lower (secrets in CI) | Higher (no external access) |
| Kubernetes Friendly | Moderate | High |
| Auditability | Medium | High |
| Rollback | Manual | Automatic |
| Scalability | Limited | High |
| Drift Detection | Weak | Strong |

---

## GitOps Workflow Diagram


::contentReference[oaicite:0]{index=0}


---

## Conclusion (Chosen GitOps Strategy)

Based on security, scalability, and cloud-native best practices, **we will implement the following GitOps strategy**:

### ✅ **Pull-based GitOps Model**
- Git as the single source of truth
- Argo CD / Flux CD–style continuous reconciliation
- No direct CI access to production clusters

### ✅ **Declarative Configuration**
- Kubernetes manifests / Helm charts stored in Git
- Version-controlled and reviewed via Merge Requests

### ✅ **Environment-wise Repositories or Directories**
- Separate configs for dev, staging, and production
- Controlled promotions via Git commits

### ❌ **What we will not use**
- Manual deployments
- Direct kubectl access to production
- Push-based GitOps for production environments

This strategy ensures **high security, auditability, reliability, and easy rollback**.

---

## Contact Information

**Author:** Ajitesh Singh  
**Role:** DevOps Engineer  
**Email:** ajitesh.singh@example.com  

---

## References

- CNCF GitOps Principles
- Argo CD Documentation
- Flux CD Documentation
- GitOps – Weaveworks
