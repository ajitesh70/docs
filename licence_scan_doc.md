# License Scanning – Detailed Documentation

---

| Author | Created On | Version | Last Updated By | Reviewer L0 | Reviewer L1 | Reviewer L2 |
|--------|------------|---------|-----------------|-------------|-------------|-------------|
| Ajitesh Singh | 05-03-2026 | v1 | Ajitesh Singh | Priyanshu | Faisal | Mahesh |

---

## Table of Contents

- [Introduction](#introduction)
- [What is License Scanning?](#what-is-license-scanning)
- [Why License Scanning?](#why-license-scanning)
- [Workflow](#workflow)
- [Different Tools](#different-tools)
- [Comparison of Tools](#comparison-of-tools)
- [Advantages](#advantages)
- [Proof of Concept (POC)](#proof-of-concept-poc)
- [Best Practices](#best-practices)
- [Recommendation & Conclusion](#recommendation--conclusion)
- [Contact Information](#contact-information)
- [References](#references)

---

## Introduction

Every open-source dependency carries a license that defines how it can be used and distributed. Failing to comply exposes organizations to legal risk. License Scanning automates the detection and compliance verification of these licenses across all project dependencies, integrated directly into the CI/CD pipeline.

---

## What is License Scanning?

License Scanning is the automated process of inspecting project dependencies to identify the open-source licenses they carry and verifying that those licenses comply with the organization's usage policies.

| Category | Details |
|---|---|
| What it scans | All direct and transitive dependencies in the project |
| What it detects | License types (MIT, Apache 2.0, GPL, LGPL, etc.) |
| What it flags | Incompatible, missing, or policy-violating licenses |
| Where it runs | Locally, in CI pipelines, or as a scheduled scan |

Common license categories:

| License Type | Example | Risk Level |
|---|---|---|
| Permissive | MIT, Apache 2.0, BSD | Low — minimal restrictions |
| Weak Copyleft | LGPL | Medium — requires sharing modifications |
| Strong Copyleft | GPL v2 / v3 | High — requires releasing source code |
| Proprietary | Commercial licenses | Varies — must be reviewed case-by-case |

---

## Why License Scanning?

| Reason | Details |
|---|---|
| Legal compliance | Using GPL-licensed code in proprietary software without compliance can result in legal action |
| Risk detection | Identifies incompatible or high-risk licenses before they reach production |
| Automated governance | Policy checks run automatically — no manual dependency audits required |
| CI gate enforcement | Blocks builds or merges when non-compliant licenses are detected |
| Audit readiness | Maintains a complete, up-to-date record of all licenses in use |

---

## Workflow

<img width="415" height="728" alt="image" src="https://github.com/user-attachments/assets/0ef8a3e8-6555-422e-8f52-b8134d0841f3" />


---

## Different Tools

| Tool | Description | Best For |
|---|---|---|
| FOSSA | Enterprise license compliance platform with CI integration, dashboard, and policy enforcement | Production CI/CD pipelines |
| ScanCode Toolkit | Open-source tool that scans codebases for licenses, copyrights, and packages | Deep offline scanning |
| WhiteSource (Mend) | Commercial SCA platform covering licenses, vulnerabilities, and outdated dependencies | Enterprise security and compliance |

---

## Comparison of Tools

| Feature | FOSSA | ScanCode | WhiteSource |
|---|---|---|---|
| CI Integration | Yes | Yes | Yes |
| Dashboard | Yes | No | Yes |
| Policy Enforcement | Yes | No | Yes |
| License Detection | Yes | Yes | Yes |
| Security Scanning | Yes | No | Yes |
| License | Free / Commercial | Open Source | Commercial |
| Best For | CI/CD compliance | Offline deep scan | Enterprise SCA |

---

## Advantages

| Advantage | Description |
|---|---|
| Early risk detection | License issues caught at commit time before reaching production |
| Automated compliance | No manual dependency audits — every build is checked automatically |
| Policy enforcement | Incompatible licenses are blocked from merging via CI gates |
| Complete visibility | Full inventory of all licenses across direct and transitive dependencies |
| Audit trail | Every scan result is logged and traceable for legal review |

---

## Proof of Concept (POC)

For the detailed POC, refer to: **[POC – License Scanning Using FOSSA](./POC-license-scanning-fossa.md)**

Results from the `salary-api` project:

| Metric | Result |
|---|---|
| Tool used | FOSSA |
| Licenses detected | Apache 2.0, MIT |
| Policy violations | 0 |
| Compliance status | Passed |

---

## Best Practices

| Practice | Details |
|---|---|
| Scan on every PR | Make license scanning a mandatory CI check, not a periodic audit |
| Define a license policy | Explicitly list allowed, restricted, and prohibited licenses for your organisation |
| Block high-risk licenses | GPL and AGPL should require legal review before approval |
| Scan transitive dependencies | Direct dependencies are only part of the picture — transitive ones carry equal risk |
| Review on every dependency update | A version bump can introduce a different license |

---

## Recommendation & Conclusion

### Recommendation

| Tool | Purpose |
|---|---|
| FOSSA | Primary license scanning and compliance platform — CI integration and dashboard |
| ScanCode Toolkit | Supplementary deep offline scanning for audits |
| Quality Gate | Block any dependency with GPL or unknown license without legal review |

### Conclusion

License Scanning is essential for managing open-source risk. **FOSSA** is the recommended tool for this project — it provides automated CI integration, policy enforcement, and dashboard visibility. The POC confirmed zero violations on `salary-api` using Apache 2.0 and MIT licenses, making it a reliable choice for enforcing compliance on every commit.

---

## Contact Information

| Name | Email |
|------|-------|
| Ajitesh Singh | ajitesh.singh.snaatak@mygurukulam.co |

---

## References

| Title | Link |
|---|---|
| FOSSA Official Documentation | https://docs.fossa.com/ |
| FOSSA CLI GitHub | https://github.com/fossas/fossa-cli |
| ScanCode Toolkit | https://github.com/nexB/scancode-toolkit |
| Open Source License Compliance | https://opensource.org/licenses |
| TLDR-Legal | https://tldrlegal.com/ |
