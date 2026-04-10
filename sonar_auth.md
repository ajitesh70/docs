# SonarQube Authentication — GitHub OAuth Implementation

---

| Author | Created On | Version | Last Updated By | Reviewer L0 | Reviewer L1 | Reviewer L2 |
|--------|------------|---------|-----------------|-------------|-------------|-------------|
| Ajitesh Singh | 11-04-2026 | v1 | Ajitesh Singh | | | |

---

## Table of Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [GitHub OAuth Configuration](#github-oauth-configuration)
- [Organization-Based Access Control](#organization-based-access-control)
- [Authentication Flow](#authentication-flow)
- [Common Issues and Fixes](#common-issues-and-fixes)
- [Key Concepts](#key-concepts)
- [Conclusion](#conclusion)
- [Contact Information](#contact-information)
- [References](#references)

---

## Overview

This document covers the setup of GitHub OAuth authentication for SonarQube, enabling secure login without manual user management and optional organization-based access control.

**What this implements:**

- GitHub OAuth login for SonarQube
- No manual user accounts required
- Organization-based access restriction (only org members can log in)

---

## Prerequisites

| Requirement | Details |
|---|---|
| SonarQube | Running and accessible via browser |
| GitHub Account | Owner or admin of the GitHub organization |
| SonarQube Admin Access | Required to configure authentication settings |

---

## GitHub OAuth Configuration

### Step 1 — Create a GitHub OAuth App

Go to: **https://github.com/settings/developers** → OAuth Apps → New OAuth App

| Field | Value |
|---|---|
| Application Name | SonarQube |
| Homepage URL | `http://<SONARQUBE-URL>:9000` |
| Authorization Callback URL | `http://<SONARQUBE-URL>:9000/oauth2/callback/github` |

After creation, copy your:
- **Client ID**
- **Client Secret**

### Step 2 — Set Server Base URL in SonarQube

> This step is critical. If not set correctly, OAuth redirects will fail silently.

```
Administration → General → Server base URL
```

Set to:

```
http://<SONARQUBE-URL>:9000
```

### Step 3 — Configure GitHub Auth in SonarQube

```
Administration → Configuration → Authentication → GitHub
```

| Setting | Value |
|---|---|
| Enable GitHub Authentication | ✅ On |
| Client ID | Paste from GitHub |
| Client Secret | Paste from GitHub |
| Allow users to sign up | ✅ On |

Click **Save**.

### Step 4 — Test Login

1. Logout from SonarQube
2. You should now see a **"Log in with GitHub"** button on the login page
3. Click it → authorize on GitHub → you will be redirected back and logged in automatically

---

## Organization-Based Access Control

To restrict access so only members of your GitHub organization can log in:

### Step 1 — Add Your Org in SonarQube

```
Administration → Configuration → Authentication → GitHub → Organizations
```

Enter your org name (exact spelling, case-sensitive):

```
your-org-name
```

### Step 2 — Make Your Org Membership Public

By default, GitHub org memberships are private. SonarQube uses the GitHub API to verify membership — if private, the API cannot confirm it and login will be denied even for org owners.

Go to:

```
https://github.com/orgs/<your-org>/people
```

Find your username → change **Private → Public**

### Step 3 — Grant Org Access During OAuth

When GitHub asks for authorization, click **Grant** next to your organization before clicking Authorize. Without this, GitHub will not send org membership data to SonarQube.

---

## Authentication Flow

```
User clicks "Log in with GitHub"
        ↓
Redirect to GitHub OAuth page
        ↓
User authorizes + grants org access
        ↓
GitHub verifies org membership (requires public membership)
        ↓
Redirect back to SonarQube
        ↓
SonarQube creates / logs in user
        ↓
Dashboard access granted
```

---

## Common Issues and Fixes

| Issue | Cause | Fix |
|---|---|---|
| GitHub login button not visible | Auth not enabled in SonarQube | Enable under Administration → Authentication → GitHub |
| "Not authorized" after GitHub login | Org membership is private | Make membership Public in GitHub org settings |
| OAuth redirect fails | Callback URL mismatch | Ensure callback URL in GitHub app exactly matches SonarQube URL |
| Server base URL not set | OAuth redirect mismatch | Set exact URL under Administration → General |
| Using `localhost` as URL | GitHub OAuth cannot redirect to localhost | Use the server's public IP or domain name |

---

## Key Concepts

### Why Public Org Membership?

GitHub's API only exposes organization membership data when that membership is set to **Public**. SonarQube queries this API to enforce org-based access control. Even as an org owner, if your membership is private, the API returns no data and SonarQube denies access.

### OAuth App vs GitHub App

| | OAuth App | GitHub App |
|---|---|---|
| Setup difficulty | Simple (10–15 min) | Complex (1–2 hours) |
| Best for | Standard CI/CD setups | Enterprise, strict org control |
| Requires private key (.pem) | No | Yes |
| Org restriction | Via config field | Built-in |

This implementation uses **OAuth App** — the standard and recommended approach for most setups.

---

## Conclusion

This implementation provides:

- **Secure login** via GitHub OAuth — no manual user accounts needed
- **Organization-based access control** — only org members can access SonarQube
- **Easy maintenance** — user access is managed through GitHub org membership

---

## Contact Information

| Name | Email |
|------|-------|
| Ajitesh Singh | ajitesh.singh.snaatak@mygurukulam.co |

---

## References

| Title | Link |
|---|---|
| SonarQube GitHub Authentication | https://docs.sonarsource.com/sonarqube/latest/instance-administration/authentication/github/ |
| GitHub OAuth Apps Documentation | https://docs.github.com/en/apps/oauth-apps |
| SonarQube Documentation | https://docs.sonarsource.com/sonarqube |
