# architecture-skills

Claude Code & Gemini Compatible skills for architecture.

This repository provides specialized skills for the Opencode environment. Currently, it includes the following skills:

## Available Skills

| Skill Name | Core Functionalities | Prerequisites |
|------------|----------------------|---------------|
| `security-methodological-reviewer` | Imposes a rigorous, phased workflow for security audits. Combines SAST/Secret scanning with mandatory SBOM generation and dependency reachability analysis. | Docker, Trivy, Target Project Package Managers (e.g., Maven, NPM) |
| `methodological-architecture-designer` | Imposes a phased workflow for architecture design from a CCTP (PDF). Guides NFR extraction and produces C4 Model diagrams (PlantUML) and ADRs. | PDF CCTP Document |

---

## Detailed Skill Documentation

### `security-methodological-reviewer`

This skill guides the user through a structured, multi-phase security development methodology, ensuring that security audits are thorough, findings are verified for reachability, and reports are highly actionable.

#### **Functionalities**

- **Phase 0: Initialization and Tool Verification:** Automatically verifies the presence of required security tools and presents a confirmation prompt before proceeding.
- **Phase 1: Security Scoping & Stack Detection:** Detects the project's technology stack (e.g., Maven, NPM) and identifies critical business logic and compliance needs.
- **Phase 2: SAST & Secrets Scanning:** Runs static analysis and secret detection utilizing Dockerized instances of tools like **Gitleaks**, **Trufflehog**, and **Semgrep**, as well as local linters like **Bandit**.
- **Phase 3: Dependency Analysis:** Generates an SBOM (Software Bill of Materials) using CycloneDX and scans it for vulnerabilities using **Trivy**.
- **Phase 4: Reachability & Impact Analysis:** Uses codebase search tools (`grep`, `read`) to determine if vulnerable dependency functions are actually imported, called, or reachable, significantly reducing false positives.
- **Phase 5: Detailed Security Report:** Generates a comprehensive vulnerability report including explicit CVE IDs, CVSS scores, project impact (Affected/Not Reachable), and actionable remediation steps.

#### **Prerequisites**

To fully utilize this skill, the host environment must have the following tools available:

1. **Docker:** Mandatory for running isolated security tools without polluting the host environment. The skill uses Docker images for:
   - `ghcr.io/gitleaks/gitleaks:latest` (Secret Detection)
   - `semgrep/semgrep` (Static Analysis)
   - `trufflesecurity/trufflehog:latest` (Advanced Secret Verification)
2. **Trivy:** Required for vulnerability scanning of SBOMs and filesystems. ([Installation Guide](https://trivy.dev/docs/latest/getting-started/installation/))
3. **Project Build Tools:** Depending on the target repository, package managers should be available for native SBOM generation and SAST:
   - **Java:** Maven (`mvn`) for CycloneDX Maven Plugin.
   - **JavaScript/Node.js:** NPM (`npx`) or ESLint for JS/TS security scanning.
   - **Python:** Python environment for `bandit` (Python SAST).

Prior running the analysis, you can pull the different docker images :

```bash
docker pull  ghcr.io/gitleaks/gitleaks:latest
docker pull semgrep/semgrep
docker pull trufflesecurity/trufflehog:latest
```

### `methodological-architecture-designer`

This skill guides the user through a structured architectural design methodology starting from a CCTP (Cahier des Clauses Techniques Particulières) provided as a PDF. It ensures rigorous extraction of requirements and produces detailed architectural artifacts.

#### **Functionalities**

- **Phase 0: Initialization & CCTP Analysis:** Ingests the provided PDF document and extracts NFRs, functional scope, security rules, and cloud provider constraints. Requires user validation before proceeding.
- **Phase 1: Architectural Strategy & Pattern Matching:** Matches requirements to architectural patterns and aligns with preferred enterprise solutions, formulating a target solution proposition for user validation.
- **Phase 2: Architectural Design:** Creates the architecture design and evaluates trade-offs based on preferred solutions.
- **Phase 3: Detailed Output Generation:** Produces a comprehensive architecture document including a requirements summary, C4 Model architecture diagrams (PlantUML), Architecture Decision Records (ADRs), technology recommendations, and risk mitigations.

#### **Prerequisites**

To fully utilize this skill, you need:
1. **CCTP Document:** A project requirement document (preferably in PDF format).
