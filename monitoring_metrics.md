# Monitoring Metrics – SonarQube Documentation

---

| Author | Created On | Version | Last Updated By | Reviewer L0 | Reviewer L1 | Reviewer L2 |
|--------|------------|---------|-----------------|-------------|-------------|-------------|
| Ajitesh Singh | 05-01-2026 | v1 | Ajitesh Singh | Priyanshu | Faisal | Mahesh |

---

## Table of Contents

- [Introduction](#introduction)
- [What is Monitoring in SonarQube?](#what-is-monitoring-in-sonarqube)
- [Why Monitoring Metrics?](#why-monitoring-metrics)
- [Workflow](#workflow)
- [Metrics to Monitor](#metrics-to-monitor)
- [Quality Gate Monitoring](#quality-gate-monitoring)
- [How to Monitor](#how-to-monitor)
- [Alerts and Thresholds](#alerts-and-thresholds)
- [Advantages](#advantages)
- [Best Practices](#best-practices)
- [Conclusion](#conclusion)
- [Contact Information](#contact-information)
- [References](#references)

---

## Introduction

Software quality is not a one-time activity — it must be continuously monitored. Monitoring metrics in SonarQube ensures that code quality, security, maintainability, and reliability standards are maintained over time. By defining measurable thresholds and alert mechanisms, teams can detect quality degradation early and prevent defective code from reaching production.

---

## What is Monitoring in SonarQube?

Monitoring in SonarQube refers to the continuous tracking of code quality indicators such as bugs, vulnerabilities, code smells, coverage, duplications, and technical debt. SonarQube provides dashboards, quality gates, and APIs that allow organizations to monitor these metrics automatically on every commit.

---

## Why Monitoring Metrics?

- Prevent production defects
- Detect security risks early
- Maintain coding standards
- Reduce technical debt
- Enforce CI/CD quality gates
- Support DevSecOps practices

Without monitoring, code quality gradually deteriorates.

---

## Workflow

<img width="487" height="838" alt="image" src="https://github.com/user-attachments/assets/c63ca479-ff9d-4a72-abea-add2fd35e623" />


---

## Metrics to Monitor

### Reliability

Reliability metrics measure how likely the code is to behave correctly at runtime. A low reliability score means the application may crash or produce wrong results in production.

| Metric | What it means | Threshold |
|---|---|---|
| **Bugs** | Lines of code that will likely cause incorrect behavior — for example, a null pointer dereference or an always-false condition. These are not warnings; they are confirmed issues. | 0 new bugs |
| **Reliability Rating** | An overall A–E grade based on the severity of bugs found. A = no bugs, E = at least one blocker bug. | Maintain A |

---

### Security

Security metrics identify code that could be exploited by attackers. Even a single unaddressed vulnerability can lead to a data breach.

| Metric | What it means | Threshold |
|---|---|---|
| **Vulnerabilities** | Code weaknesses that can be directly exploited — for example, SQL injection, hardcoded passwords, or insecure deserialization. | 0 |
| **Security Rating** | An A–E grade based on the severity of vulnerabilities found. A = no vulnerabilities. | Maintain A |
| **Security Hotspots** | Code that is not necessarily a vulnerability but requires a human to review and decide — for example, use of a weak hash algorithm. SonarQube flags it; a developer must mark it as safe or fix it. | Must be reviewed |

---

### Maintainability

Maintainability metrics measure how easy the code is to understand, modify, and extend. Poor maintainability means future changes will be slower and riskier.

| Metric | What it means | Threshold |
|---|---|---|
| **Code Smells** | Patterns in code that are not bugs but make the code harder to maintain — for example, methods that are too long, too many parameters, or duplicated logic. Left unaddressed, they accumulate into serious problems. | No critical smells |
| **Technical Debt** | The estimated time it would take a developer to fix all code smells. Expressed as hours or as a percentage of the total development time invested. A high debt ratio means the codebase is becoming expensive to maintain. | < 5% debt ratio |
| **Maintainability Rating** | An A–E grade based on the technical debt ratio. A = debt ratio below 5%. | Maintain A |

---

### Coverage

Coverage metrics show how much of the source code is actually executed when the test suite runs. Low coverage means large parts of the application are untested and bugs there will go undetected.

| Metric | What it means | Threshold |
|---|---|---|
| **Line Coverage** | The percentage of individual lines of code that were executed at least once during tests. If a line is never run by any test, bugs in that line will not be caught. | > 60% |
| **Branch Coverage** | The percentage of decision branches (if/else, switch cases) that were tested for both outcomes. A branch that is never tested in its "false" path may hide bugs. | > 50% |
| **New Code Coverage** | Coverage measured only on code written since the last analysis. This focuses the team on keeping new work well-tested rather than being distracted by legacy gaps. | > 80% |

---

### Duplication

Duplication metrics identify copy-pasted code. Duplicated code means a bug fix or change must be made in multiple places — increasing the risk of inconsistency.

| Metric | What it means | Threshold |
|---|---|---|
| **Duplicated Lines** | The percentage of lines that appear identically in two or more places in the codebase. | < 3% |
| **Duplicated Blocks** | Blocks of code (typically 10+ lines) that are repeated. Each duplicated block is a maintenance liability. | 0 critical |

---

## Quality Gate Monitoring

A Quality Gate enforces thresholds automatically on every analysis. A recommended baseline gate:

- No new bugs
- No new vulnerabilities
- Coverage on new code > 80%
- Duplications on new code < 3%
- Maintainability rating = A

If any condition fails, the build fails and the merge is blocked.

---

## How to Monitor

**SonarQube Dashboard** — Web UI shows project overview, historical trends, and quality gate status.

**CI/CD Integration** — Use the Quality Gate step in Jenkins to fail the build automatically:

```groovy
waitForQualityGate abortPipeline: true
```

**Webhooks** — Configure SonarQube webhooks to notify Slack, Microsoft Teams, or Jenkins when a quality gate fails.

**Email Notifications** — SonarQube can alert users when a quality gate fails or an issue is assigned.

**Prometheus + Grafana** — SonarQube exposes metrics via `/api/system/health`. Prometheus can scrape and Grafana can visualize trends over time.

---

## Alerts and Thresholds

Alert conditions to configure:

- New critical vulnerability detected
- Coverage drops below 50%
- Technical debt increases by more than 10%
- Reliability rating changes from A to B
- Duplicated code exceeds 5%

Alert mechanisms:

- CI pipeline failure
- Slack notification
- Email alert
- Webhook trigger

---

## Advantages

- Prevents poor-quality code from reaching deployment
- Enables early security detection
- Enforces development discipline across all contributors
- Improves long-term maintainability
- Supports the DevSecOps model
- Provides measurable quality KPIs for teams and management

---

## Best Practices

- Focus on **new code** metrics rather than legacy issues
- Never allow new critical bugs or vulnerabilities to pass
- Set realistic coverage thresholds — do not chase 100% blindly
- Integrate SonarQube with CI/CD as a hard quality gate
- Monitor trends over time, not just point-in-time snapshots
- Review Security Hotspots manually — automation cannot replace judgement
- Use branch analysis for feature branches before merging

---

## Conclusion

Monitoring metrics in SonarQube is essential for maintaining continuous code quality. By defining clear thresholds and integrating automated quality gates into CI/CD pipelines, teams can ensure secure, reliable, and maintainable software. Continuous monitoring prevents technical debt accumulation and promotes engineering excellence across the entire development lifecycle.

---

## Contact Information

| Name | Email |
|------|-------|
| Ajitesh Singh | ajitesh.singh.snaatak@mygurukulam.co |

---

## References

| Title | Link |
|---|---|
| SonarQube Official Documentation | https://docs.sonarsource.com/sonarqube/latest/ |
| SonarSource Metric Definitions | https://docs.sonarsource.com/sonarqube/latest/user-guide/metric-definitions/ |
| SonarQube Quality Gates | https://docs.sonarsource.com/sonarqube/latest/user-guide/quality-gates/ |
| JaCoCo Documentation | https://www.jacoco.org/jacoco/trunk/doc/ |
| OWASP Secure Coding Guidelines | https://owasp.org/www-project-secure-coding-practices-quick-reference-guide/ |
