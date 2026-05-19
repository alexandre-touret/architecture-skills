---
name: security-methodological-reviewer
description: A specialized skill that imposes a rigorous, phased workflow for security audits. It combines SAST/Secret scanning with mandatory SBOM generation and dependency reachability analysis to ensure findings are actionable and relevant.
---
# Skill: security-methodological-reviewer

## Description
A specialized skill that imposes a rigorous, phased workflow for security audits. It combines SAST/Secret scanning with mandatory SBOM generation and dependency reachability analysis to ensure findings are actionable and relevant.

## When to Use
- Before a major release or production deployment.
- When auditing a new or legacy codebase for security vulnerabilities.
- To perform a structured dependency analysis using SBOMs and Trivy.

## Instructions

You are a skill that guides the user through a rigorous security development methodology. You must follow this process step by step, starting with a mandatory tool verification and user confirmation.

### Phase 0: Initialization and Tool Verification

At the very beginning of the skill execution, you **MUST** perform a tool verification, present a summary table with installation links, and **WAIT for user confirmation**.

1. **Verify Environment**:
   - Check current working directory.
   - Run `trivy --version` to check availability.
   - Run ` docker --version` to check Docker availability
   - Run `docker pull ghcr.io/gitleaks/gitleaks:latest` to check gitleaks availability
   - Run `docker pull semgrep/semgrep && docker run --rm semgrep/semgrep semgrep --version` to check Semgrep availability
   - Run the commands described in the [REFERENCE](./REFERENCE.md) to check Gitleaks, semgrep and trufflehog

2. **Tools Summary Table**:
   Display this table to the user to confirm the security environment is ready:

| Tool | Status | Purpose | Installation Link |
| :--- | :--- | :--- | :--- |
| **Trivy** | [Found/Not Found] | Vulnerability scanning | [Install Trivy](https://aquasecurity.github.io/trivy/latest/getting-started/installation/) |
| **Gitleaks** | [Found/Not Found] | Secret detection | [Install Gitleaks](https://github.com/gitleaks/gitleaks#installing) |
| **Semgrep** | [Found/Not Found] | Static Analysis (SAST) | [Install Semgrep](https://semgrep.dev/docs/getting-started/) |
| **CycloneDX** | [N/A - Plugin] | SBOM generation | [Maven Plugin](https://github.com/CycloneDX/cyclonedx-maven-plugin) / [NPM Tool](https://github.com/CycloneDX/cyclonedx-node-module) |
| **Internal Tools**| Ready | Grep, Glob, Read, AskUserQuestion | N/A |

3. **Mandatory Confirmation Pause**:
   You **MUST** use **AskUserQuestion** to ask the user if they want to proceed:
   - "Some security tools might be missing. Do you want to proceed with the available tools, or would you like to stop to install the missing ones?"
   - **DO NOT** proceed to Phase 1 until the user explicitly confirms.

**YOU MUST refer to the [Reference Guide](./REFERENCE.md) to run every tool**

### Phase 1: Security Scoping & Stack Detection

Start by gathering information and detecting the environment:

1. **Stack Detection**: Use **Glob** to identify if the project uses Maven (`pom.xml`), Gradle (`build.gradle`), or NPM (`package.json`).
2. **User Questions**: Use **AskUserQuestion** to identify critical business logic (Crown Jewels), compliance needs (OWASP, etc.), and scanning preferences.

### Phase 2: SAST & Secrets Scanning

Unless disabled, run static analysis and secret detection using **Bash**:

1. **Secrets**: `gitleaks detect --source=.` or `trufflehog filesystem .`.
2. **SAST**: 
   - Python: `bandit -r .`
   - Generic/Polyglot: `semgrep --config=auto .`
   - JS/TS: `eslint . --plugin security`

### Phase 3: Dependency Analysis (SBOM & Trivy)

Perform a mandatory dependency audit using **Bash**:

1. **SBOM Generation**:
   - **Maven**: `mvn org.cyclonedx:cyclonedx-maven-plugin:makeAggregateBom` (produces `target/bom.json`).
   - **NPM**: `npx @cyclonedx/cyclonedx-npm --output-format JSON --output-file bom.json`.
2. **Trivy Scan**: Run `trivy sbom bom.json --severity HIGH,CRITICAL`. If SBOM generation fails, fallback to `trivy fs .`.

### Phase 4: Reachability & Impact Analysis

For every dependency vulnerability found, you **must** perform a check to see if the project is actually affected:
1. **Usage Check**: Use **Grep** and **Read** to see if the vulnerable library function or class is actually imported and called.
2. **Configuration Check**: Check if the vulnerable feature is enabled in project configurations.
3. **Status Assignment**: Label as `Affected`, `Not Reachable`, or `Needs Investigation`.

### Phase 5: Detailed Security Report

Produce a report using the exact format. You **MUST** include **ALL** findings from the previous phases in this report. Do not summarize or provide only a sample:

| Field | Description |
| :--- | :--- |
| **ID** | CVE ID (e.g., CVE-2023-1234) |
| **Status** | Severity level (Low, Medium, High, Critical) |
| **CVSS** | Full CVSS vector or score (e.g., 9.8 / CVSS:3.1/AV:N/...) |
| **Label** | Concise name of the vulnerability |
| **Explanation** | Detailed description of the flaw and its potential impact. |
| **Project Impact** | **[AFFECTED / NOT REACHABLE / UNKNOWN]** + Justification based on Phase 4 analysis. |
| **Remediation** | Specific steps to fix (e.g., upgrade to version X.Y.Z). |

---

### Strict Rules
- ❌ **NEVER** skip the Reachability Analysis for dependencies.
- ❌ **NEVER** report a tool's output without verifying its context in the code.
- ❌ **NEVER** summarize or provide only a sample of the findings in Phase 5. All findings must be explicitly listed.
- ✅ **ALWAYS** start with the Phase 0 tool verification table and **wait for user confirmation**.
- ✅ **ALWAYS** provide the full CVSS code for every CVE.
- ✅ **ALWAYS** ask for confirmation before running heavy build commands (like `mvn`).
- ✅ **ALWAYS** refer to [REFERENCE.md](./REFERENCE.md) for tool-specific guidance.

### Communication Format
Use the `=== PHASE [N] ===` header to track progress and wait for user validation after Phase 3 and Phase 4.
