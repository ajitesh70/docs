# POC – License Scanning Using FOSSA

<p align="center">
  <img width="486" height="192" alt="image" src="https://github.com/user-attachments/assets/8df5e5af-8985-454d-b388-b6fe6acfd362" />

</p>
---

| Author | Created On | Version | Last Updated By | Reviewer L0 | Reviewer L1 | Reviewer L2 |
|--------|------------|---------|-----------------|-------------|-------------|-------------|
| Ajitesh Singh | 05-03-2026 | v1 | Ajitesh Singh | Priyanshu | Faisal | Mahesh |

---

## Table of Contents

- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
- [License Scanning Document](#license-scanning-document)
- [Step 1 – Set Up FOSSA](#step-1--set-up-fossa)
- [Step 2 – Configure FOSSA Integration](#step-2--configure-fossa-integration)
- [Step 3 – Run License Scanning](#step-3--run-license-scanning)
- [Step 4 – Analyze and Resolve Issues](#step-4--analyze-and-resolve-issues)
- [Conclusion](#conclusion)
- [Contact Information](#contact-information)
- [References](#references)

---

## Introduction

Modern software applications rely heavily on open-source libraries and dependencies. These dependencies come with specific licenses that define how the software can be used, modified, and distributed. License scanning is an important DevSecOps practice that helps organizations ensure compliance with open-source license policies.

This POC demonstrates how FOSSA can be integrated into a project to automatically scan dependencies and identify potential license compliance issues.

---

## Prerequisites

| Requirement | Details |
|---|---|
| FOSSA Account | Required for dashboard access and API key generation |
| Project Repository | Must contain dependencies to scan |
| Linux / CI Environment | Machine to run FOSSA CLI |
| Git | Installed on the system |
| Terminal Access | Basic command line knowledge required |

> Project used for this POC: `salary-api`

---

## License Scanning Document

For a detailed understanding of License Scanning concepts refer to the **[License Scanning Documentation](./license-scanning-documentation.md)** which includes Introduction, Importance, Workflow, Tools, Comparison, Best Practices, and Recommendations.

---

## Step 1 – Set Up FOSSA

1. Navigate to [https://fossa.com/signup](https://fossa.com/signup)
2. Create an account using email or GitHub authentication
3. Log in to the FOSSA Dashboard
4. After logging in, generate an API key from **Settings → API Keys → Generate New API Key**

<img width="609" height="682" alt="image" src="https://github.com/user-attachments/assets/47bedf41-4158-485f-8a2d-b61d90efeeea" />


---

## Step 2 – Configure FOSSA Integration

### Install FOSSA CLI

```bash
curl -H 'Cache-Control: no-cache' https://raw.githubusercontent.com/fossas/fossa-cli/master/install-latest.sh | bash
```
<img width="1912" height="230" alt="Screenshot 2026-03-05 220556" src="https://github.com/user-attachments/assets/8ffd5f36-a566-4954-ade8-7cd2995f3f53" />

Verify the installation:

```bash
fossa --version
```


Expected output:

```
fossa-cli version 3.x.x
```

<img width="881" height="83" alt="Screenshot 2026-03-05 220611" src="https://github.com/user-attachments/assets/23cf222c-4d3c-401d-9928-72cc96b9ccf0" />

### Authenticate Using API Key

```bash
export FOSSA_API_KEY=<YOUR_API_KEY>
```

<img width="784" height="32" alt="image" src="https://github.com/user-attachments/assets/97c303d7-aa15-499b-9bb2-b7965e51a901" />


## Step 3 – Run License Scanning

Navigate to the project directory:

```bash
cd salary-api
```

Run the license analysis:

```bash
fossa analyze
```

This command:

- Detects all project dependencies
- Identifies licenses associated with each dependency
- Uploads scan results to the FOSSA dashboard

Expected output:

```
Using project name: salary-api
Using revision: 79356799a4e74ff24c7bda42371bbece4f35d4d7

View FOSSA Report:
https://app.fossa.com/projects/...
```

<img width="967" height="260" alt="Screenshot 2026-03-05 220729" src="https://github.com/user-attachments/assets/46073bf4-0bf2-446f-af20-4dda72b1e985" />

<img width="1867" height="250" alt="Screenshot 2026-03-05 220741" src="https://github.com/user-attachments/assets/e57de45f-9422-48ee-aa93-999b6013c0d4" />

---

## Step 4 – Analyze and Resolve Issues

After analysis completes, open the project in the FOSSA dashboard. The dashboard displays detected dependencies, associated licenses, compliance status, and risk indicators.

### Licenses Detected in This POC

| License | Description |
|---|---|
| Apache License 2.0 | Permissive license allowing modification and redistribution |
| MIT License | Highly permissive license allowing reuse with minimal restrictions |

Both licenses are considered safe and compliant for most software projects.

<img width="1053" height="259" alt="Screenshot 2026-03-05 221128" src="https://github.com/user-attachments/assets/2cfd8d05-0b0e-4c5f-8652-c03fb81f3498" />


### Verify Compliance

```bash
fossa test
```

Expected output:

```
Test passed! 0 issues found
```

<img width="900" height="205" alt="Screenshot 2026-03-05 220851" src="https://github.com/user-attachments/assets/5e2f77d7-fa33-4775-8df1-369eeaf687cd" />

<img width="1823" height="755" alt="Screenshot 2026-03-05 221052" src="https://github.com/user-attachments/assets/073461dc-49e8-4c36-afcf-ffe73b4ad195" />


---

## Conclusion

FOSSA was successfully integrated to perform automated license scanning on the `salary-api` project. The scan identified Apache 2.0 and MIT licenses across project dependencies — both widely accepted permissive licenses with no compliance violations. Integrating license scanning into the CI/CD pipeline helps teams detect license risks early and maintain compliance with open-source usage policies.

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
| Open Source License Compliance | https://opensource.org/licenses |
