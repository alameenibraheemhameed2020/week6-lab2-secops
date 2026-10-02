# Week 6 Lab 2: CI/CD Architecture & DevSecOps Threat Modeling

## 1. Pipeline Sequence Diagram (Quality Gates & SAST)
```mermaid
sequenceDiagram
    autonumber
    actor Dev as Developer
    participant Git as GitHub Actions
    participant SAST as SAST Security Scanner
    participant Trivy as Trivy Container Scanner
    
    Dev->>Git: Push Code / Open PR
    Git->>SAST: Run Static Analysis & Linting
    SAST-->>Git: Pass (Zero Critical Vulnerabilities)
    Git->>Git: Build Docker Container Image
    Git->>Trivy: Scan Image for Vulnerabilities
    Trivy-->>Git: Vulnerability Threshold Met (Exit Code 0)
    Git->>Registry: Push & Tag Secure Artifact
graph TD
    Developer -->|Code Push / PR| GitRepo[GitHub Repository]
    GitRepo -->|Trigger CI Pipeline| Actions[GitHub Actions Runner]
    Actions -->|Download Dependencies| ExtRepo[(External Package Repositories)]
    Actions -->|Build & Scan Image| Trivy[Trivy Security Scanner]
    Trivy -->|Output Execution Report| Artifacts[Container Registry / Logs]

    subclassDef threat fill:#ffe6e6,stroke:#ff4d4d,stroke-width:2px;
    class ExtRepo,Artifacts threat;
## 3. STRIDE Threat Model Matrix & Mitigations

| Threat Category | Target Component | Potential Attack Vector | Security Mitigation |
| :--- | :--- | :--- | :--- |
| **Spoofing** | Git Commit / PR Author | Impersonating a legitimate developer to inject malicious code | Enforce SSH key signing, GPG commit verification, and strict GitHub access controls. |
| **Tampering** | Dependency Stream / Registry | Injecting malicious packages or modifying container layers in transit | Dependency pinning (`package-lock.json`), checksum verification, and signed container images. |
| **Repudiation** | Build & Deployment Logs | Actors denying unauthorized modifications or pipeline manipulation | Centralized immutable audit logging via GitHub Actions workflow history. |
| **Information Disclosure** | Build Environment / Logs | Accidental exposure of API keys, tokens, or cloud secrets in logs | Secret masking, encrypted GitHub Secrets, and automated secret scanning. |
| **Denial of Service** | CI/CD Runner / Server | Overwhelming pipeline queues with infinite loops or resource-heavy builds | Setting strict job timeout limits and concurrency limits on runners. |
| **Elevation of Privilege** | Branch Protection / RBAC | Unauthorized users bypassing code reviews to push straight to production | Mandatory branch protections, multi-person code reviews, and required status checks. |
