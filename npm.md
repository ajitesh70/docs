# NPM Documentation (Node Package Manager)

| Author | Created On | Version | Last Updated By | Reviewer L0 | Reviewer L1 | Reviewer L2 |
|--------|------------|---------|------------------|-------------|-------------|-------------|
| Ajitesh Singh | 21-01-2026 | v1 | Ajitesh Singh | Komal Jaiswal | Akshit Kapil | Mahesh |

---

##  Table of Contents
- [Introduction](#introduction)
- [What is NPM](#what-is-npm)
- [Why NPM](#why-npm)
- [Key Features](#key-features)
- [System Requirements](#system-requirements)
- [Installation](#installation)
- [NPM Architecture](#npm-architecture)
- [package.json Explained](#packagejson-explained)
- [Dependency Types](#dependency-types)
- [Versioning (SemVer)](#versioning-semver)
- [Lock Files](#lock-files)
- [NPM Scripts](#npm-scripts)
- [Cache and Registry](#cache-and-registry)
- [Security Best Practices](#security-best-practices)
- [Common Commands](#common-commands)
- [Troubleshooting](#troubleshooting)
- [Best Practices](#best-practices)
- [Contact Information](#contact-information)
- [References](#references)

---

## Introduction
This document provides a detailed overview of **NPM (Node Package Manager)** including its purpose, architecture, installation, configuration, and best practices. The purpose of this documentation is to standardize how NPM is installed, configured, and used across development and production environments to ensure consistent builds, secure dependency management, and reliable automation.



---

## What is NPM
NPM is a package manager for JavaScript that allows developers to install, manage, and publish packages. It comes bundled with Node.js and uses the public NPM registry to distribute open-source libraries.

NPM maintains metadata in a file called `package.json`.

---

## Why NPM
- Simplifies dependency management  
- Ensures consistent builds  
- Automates project tasks  
- Provides access to large open-source ecosystem  
- Supports CI/CD pipelines  
- Enables version control of libraries  

---

## Key Features
- Dependency resolution  
- Version locking  
- Script execution  
- Global and local installs  
- Secure package publishing  
- Registry management  

---

## System Requirements

| Component | Minimum |
|------------|----------|
| OS | Linux / Windows / macOS |
| Node.js | 16+ |
| Disk | 1 GB free |
| Internet | Required |

---

## Installation

### Install Node.js (includes NPM)
```bash
sudo apt update
sudo apt install nodejs npm -y
```

Verify:
```bash
node -v
npm -v
```

---

## NPM Architecture

<img width="1075" height="362" alt="image" src="https://github.com/user-attachments/assets/d98a190a-fb4c-42b2-a238-8b51eea9c2c9" />



---

## package.json Explained

| Field | Description |
|--------|-------------|
| name | Project name |
| version | Project version |
| dependencies | Runtime libraries |
| devDependencies | Development libraries |
| scripts | Automation commands |

---

## Dependency Types

| Type | Usage |
|------|--------|
| dependencies | Production |
| devDependencies | Development |
| peerDependencies | Plugin compatibility |
| optionalDependencies | Optional features |

---

## Versioning (SemVer)

Format:
```
MAJOR.MINOR.PATCH
```

Example:
```
2.4.1
```

---

## Lock Files

| File | Purpose |
|------|----------|
| package-lock.json | Locks exact versions |
| npm-shrinkwrap.json | Production locking |

---

## NPM Scripts

Example:
```json
"scripts": {
  "start": "node app.js",
  "test": "jest"
}
```

Run:
```bash
npm run start
```

---

## Cache and Registry

Check cache:
```bash
npm cache verify
```

Registry:
```bash
npm config get registry
```

---

## Security Best Practices
- Use `npm audit`
- Avoid untrusted packages
- Lock dependencies
- Use private registries
- Regular upgrades

---

## Common Commands

```bash
npm init
npm install
npm update
npm uninstall
npm list
npm audit
```

---

## Troubleshooting

| Issue | Cause | Solution |
|-------|--------|-----------|
| Install fails | Network | Check internet |
| Version conflict | Dependency clash | Update versions |
| Permission error | Global install | Use sudo |
| Slow install | Cache issue | Clean cache |

---

## Best Practices
- Commit package-lock.json  
- Use semantic versioning  
- Avoid global installs  
- Use scripts  
- Regular audits  

---

## Contact Information

| Name | Email |
|------|-------|
| Ajitesh Singh | ajitesh.singh.snaatak@mygurukulam.co |

---

## References

| Link | Description |
|------|-------------|
| https://docs.npmjs.com | Official NPM Documentation |
| https://nodejs.org | Node.js Documentation |
| https://www.npmjs.com | NPM Registry |

---
