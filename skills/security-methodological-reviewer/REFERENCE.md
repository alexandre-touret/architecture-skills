# Tool Reference for Security Methodological Reviewer

This reference document describes the primary tools used during the security methodological review process and provides installation guidance.

## 🛠️ Security Tools

| Tool | Purpose | Installation Link |
| :--- | :--- | :--- |
| **Trivy** | Vulnerability scanning for SBOMs and filesystems | [Install Trivy](https://trivy.dev/docs/latest/getting-started/installation/) |
| **Gitleaks** | Secret detection in the codebase history | [Install Gitleaks](https://github.com/gitleaks/gitleaks#installing) |
| **Trufflehog** | Advanced secret scanning and verification | [Install Trufflehog](https://github.com/trufflesecurity/trufflehog#installation) |
| **Semgrep** | Static Analysis Security Testing (SAST) | [Install Semgrep](https://semgrep.dev/docs/getting-started/) |
| **Bandit** | Security linter for Python code | [Install Bandit](https://github.com/PyCQA/bandit#installation) |
| **CycloneDX Maven** | Generate SBOM for Java/Maven projects | [Maven Plugin](https://github.com/CycloneDX/cyclonedx-maven-plugin) |
| **CycloneDX NPM** | Generate SBOM for JavaScript/NPM projects | [NPM Tool](https://github.com/CycloneDX/cyclonedx-node-module) |
| **Grep** | Internal tool for reachability analysis | N/A |
| **Read** | Internal tool for code impact assessment | N/A |

## 📖 Detailed Tool Usage

### Phase 0: Initialization and Tool Verification

#### Gitleaks Usage
You MUST run Gitleaks using Docker 

```bash
docker pull ghcr.io/gitleaks/gitleaks:latest
docker run -v ${path_to_host_folder_to_scan}:/path ghcr.io/gitleaks/gitleaks:latest [COMMAND] [OPTIONS] [SOURCE_PATH]
```

#### Semgrep
You MUST run Semgrep using Docker 
```bash
docker pull semgrep/semgrep
docker run --rm semgrep/semgrep semgrep --version
```

#### Trufflehog
You MUST run Trufflehog using Docker 

```bash
docker run --rm -it -v "$PWD:/pwd" trufflesecurity/trufflehog:latest github --repo https://github.com/trufflesecurity/test_keys
```


### Phase 1: Security Scoping & Stack Detection
- **AskUserQuestion**: Interactively identifies critical business logic (Crown Jewels), compliance needs, and scanning preferences.
- **Glob / Read**: Detects project stack (Maven, NPM, etc.) by checking for `pom.xml`, `package.json`, etc.

### Phase 2: SAST & Secrets Scanning
- **Bash (gitleaks/trufflehog)**: Identifies leaked secrets (API keys, passwords) in the repository history or filesystem.
- **Bash (bandit/semgrep/eslint)**: Performs static analysis to find common security patterns and vulnerabilities.

#### Gitleaks Usage
You MUST run Gitleaks using Docker 

```bash
docker pull ghcr.io/gitleaks/gitleaks:latest
docker run -v ${path_to_host_folder_to_scan}:/path ghcr.io/gitleaks/gitleaks:latest [COMMAND] [OPTIONS] [SOURCE_PATH]
```

#### Semgrep
You MUST run Semgrep using Docker 
```bash
docker pull semgrep/semgrep
docker run --rm semgrep/semgrep semgrep --version
```

#### Trufflehog
You MUST run Trufflehog using Docker 

```bash
docker run --rm -it -v "$PWD:/pwd" trufflesecurity/trufflehog:latest github --repo https://github.com/trufflesecurity/test_keys
```

### Phase 3: Dependency Analysis (SBOM & Trivy)
- **Bash (CycloneDX)**: Generates a standard SBOM (`bom.json`) for the project's dependencies.
- **Bash (trivy)**: Scans the generated SBOM or the project filesystem for known CVEs.

### Phase 4: Reachability & Impact Analysis
- **Grep**: Searches for imports and calls to vulnerable library functions.
- **Read**: Analyzes configuration files and usage patterns to determine if the project is actually affected.
- **TodoWrite**: (Optional) Tracks the status of each investigation (Affected, Not Reachable, Unknown).

### Phase 5: Detailed Security Report
- **AskUserQuestion**: Presents the final report for user review and validation.
- **Bash**: (Optional) Can be used to export the final report to a file.
