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
