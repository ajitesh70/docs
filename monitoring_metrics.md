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
- [Best Practices](#best-practices)
- [Conclusion](#conclusion)
- [Contact Information](#contact-information)
- [References](#references)

---

## Introduction

Software quality must be continuously monitored, not just checked once. Monitoring SonarQube metrics ensures code quality, security, and maintainability standards are upheld on every commit — catching degradation before it reaches production.

---

## What is Monitoring in SonarQube?

It is the continuous tracking of code quality indicators — bugs, vulnerabilities, code smells, coverage, duplications, and technical debt — through SonarQube dashboards, quality gates, and APIs on every analysis run.

---

## Why Monitoring Metrics?

- Prevent production defects
- Detect security risks early
- Reduce technical debt
- Enforce CI/CD quality gates
- Support DevSecOps practices

---

## Workflow

```
Developer commits code
         |
         v
CI/CD Pipeline · Maven build + tests
         |
         v
JaCoCo generates coverage report
         |
         v
SonarQube analysis runs
         |
         v
Metrics calculated · Quality Gate evaluated
         |
    +----+----+
    |         |
  PASS       FAIL
 Deploy   Alert / Block
         |
         v
Continuous monitoring dashboard
```

---

## Metrics to Monitor

### Reliability — *Is the code correct at runtime?*

| Metric | What it means | Threshold |
|---|---|---|
| **Bugs** | Confirmed coding errors that will cause incorrect behavior e.g. null pointer, always-false condition | 0 new bugs |
| **Reliability Rating** | A–E grade based on bug severity. A = no bugs | Maintain A |

### Security — *Can the code be exploited?*

| Metric | What it means | Threshold |
|---|---|---|
| **Vulnerabilities** | Weaknesses directly exploitable by attackers e.g. SQL injection, hardcoded credentials | 0 |
| **Security Rating** | A–E grade based on vulnerability severity. A = none found | Maintain A |
| **Security Hotspots** | Risky code patterns that need human review — not confirmed vulnerabilities, but must not be ignored | Must be reviewed |

### Maintainability — *How easy is the code to change?*

| Metric | What it means | Threshold |
|---|---|---|
| **Code Smells** | Bad patterns that make code hard to maintain e.g. methods too long, too many parameters | No critical smells |
| **Technical Debt** | Estimated time to fix all code smells. High debt = expensive future changes | < 5% debt ratio |
| **Maintainability Rating** | A–E grade based on debt ratio. A = debt ratio below 5% | Maintain A |

### Coverage — *How much code is tested?*

| Metric | What it means | Threshold |
|---|---|---|
| **Line Coverage** | % of code lines executed during tests. Untested lines hide bugs | > 60% |
| **Branch Coverage** | % of if/else decision paths tested. Untested branches hide logic bugs | > 50% |
| **New Code Coverage** | Coverage on newly written code only — keeps new work well tested | > 80% |

### Duplication — *Is code being copy-pasted?*

| Metric | What it means | Threshold |
|---|---|---|
| **Duplicated Lines** | % of lines repeated across the codebase. A fix must be applied in multiple places | < 3% |
| **Duplicated Blocks** | Repeated blocks of 10+ lines — each one is a maintenance liability | 0 critical |

---

## Quality Gate Monitoring

A Quality Gate automatically enforces thresholds on every analysis. Recommended baseline:

- No new bugs
- No new vulnerabilities
- New code coverage > 80%
- Duplications on new code < 3%
- Maintainability rating = A

If any condition fails, the build fails and the merge is blocked.

---

## How to Monitor

| Method | Details |
|---|---|
| **SonarQube Dashboard** | Web UI — project overview, trends, quality gate status |
| **CI/CD Integration** | `waitForQualityGate abortPipeline: true` in Jenkins |
| **Webhooks** | Notify Slack, Teams, or Jenkins on quality gate failure |
| **Email Notifications** | Alert on gate failure or issue assignment |
| **Prometheus + Grafana** | Scrape `/api/system/health` and visualize metric trends |

---

## Alerts and Thresholds

| Condition | Alert Mechanism |
|---|---|
| New critical vulnerability | CI pipeline failure + Slack |
| Coverage drops below 50% | Email alert |
| Technical debt increases > 10% | Webhook trigger |
| Reliability rating drops from A to B | Slack notification |
| Duplications exceed 5% | CI pipeline failure |

---

## Best Practices

- Focus on new code metrics — do not be blocked by legacy issues
- Never allow new critical bugs or vulnerabilities to pass the gate
- Set realistic coverage thresholds — do not chase 100% blindly
- Review Security Hotspots manually — automation cannot replace judgement
- Monitor trends over time, not just point-in-time results

---

## Conclusion

Monitoring SonarQube metrics continuously, with clear thresholds and automated quality gates, ensures every commit meets the team's quality standard. It prevents technical debt accumulation, enforces security, and keeps codebases maintainable as they grow.

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
