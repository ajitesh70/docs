# VCS Authentication – Demo

---

| Author | Created On | Version | Last Updated By | Reviewer L0 | Reviewer L1 | Reviewer L2 |
|--------|------------|---------|-----------------|-------------|-------------|-------------|
| Ajitesh Singh | 16-03-2026 | v1 | Ajitesh Singh | Priyanshu | Faisal | Mahesh |

---

## Table of Contents

- [Introduction](#introduction)
- [Manual Access Provisioning with 2FA/MFA](#manual-access-provisioning-with-2famfa)
  - [1. Provisioning Users Manually](#1-provisioning-users-manually)
  - [2. Enforcing MFA/2FA](#2-enforcing-mfa2fa)
  - [3. Mandatory 2FA Enforcement – User Access Behaviour](#3-mandatory-2fa-enforcement--user-access-behaviour)
- [Contact Information](#contact-information)
- [References](#references)

---

## Introduction

This document describes the authentication setup for the Version Control System. Since GitHub SAML SSO is only available on GitHub Enterprise and not supported on Free plans, SSO cannot be enforced. Instead, mandatory Two-Factor Authentication (2FA/MFA) has been implemented to secure user access and support manual access provisioning.

---

## Manual Access Provisioning with 2FA/MFA

### 1. Provisioning Users Manually

Admins manually invite users via:

```
Organisation → People → Invite Member
```

<img width="943" height="691" alt="image" src="https://github.com/user-attachments/assets/fb744b7b-443b-4160-bbbf-53a1544cc02e" />


---

### 2. Enforcing MFA/2FA

Navigate to:

```
Organisation → Settings → Security → Two-factor authentication
```

Enable:
- Require two-factor authentication for everyone
- Only allow secure two-factor methods

<img width="1234" height="544" alt="image" src="https://github.com/user-attachments/assets/85a58457-dabe-4aba-b28b-1e2dfb605d10" />


---

### 3. Mandatory 2FA Enforcement – User Access Behaviour

When 2FA is enforced, members without it configured are blocked from accessing the organisation until they set it up.

<img width="568" height="761" alt="image" src="https://github.com/user-attachments/assets/df8256c4-a32b-43a1-b19a-a2e0883c35a6" />


Users set up 2FA by scanning the QR code via an authenticator app:

<img width="1071" height="808" alt="image" src="https://github.com/user-attachments/assets/f2eeab9a-2e77-4dd7-9bb8-ea63722c0cd0" />


<img width="730" height="441" alt="Screenshot 2026-03-25 133355" src="https://github.com/user-attachments/assets/b2bec78e-ef20-4e76-9e8c-03f4a4ccfd20" />


---

## Contact Information

| Name | Email |
|------|-------|
| Ajitesh Singh | ajitesh.singh.snaatak@mygurukulam.co |

---

## References

| Title | Link |
|---|---|
| GitHub Two-Factor Authentication | https://docs.github.com/en/authentication/securing-your-account-with-two-factor-authentication-2fa |
| GitHub SAML SSO (Enterprise) | https://docs.github.com/en/enterprise-cloud@latest/organizations/managing-saml-single-sign-on-for-your-organization |
