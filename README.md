# Secure Cloud CI/CD Pipeline

A production‑grade DevSecOps reference implementation that automatically builds, scans, deploys, and self‑heals a containerised workload on AWS which can be altered to be Cloud agnostic. 

## Executive Summary

This project demonstrates an enterprise‑aligned software‑supply‑chain architecture that integrates image hardening, policy‑driven enforcement, and automated rollback across the full delivery lifecycle. The pipeline is optimised for highly regulated environments that demand continuous compliance without sacrificing release velocity.

## Core Objectives

| Objective | Outcome |
|-----------|---------|
| **Shift‑Left Security** | Mandatory vulnerability scanning (Trivy) blocks CRITICAL/HIGH CVEs pre‑deployment |
| **Least‑Privilege Access** | Distinct IAM roles for build, registry, and runtime with scoped permissions |
| **Continuous Delivery** | Zero‑touch deployments to ECS Fargate on every push to `main` |
| **Policy‑Based Rollback** | Automatic reversion to last known‑good task definition on pipeline failure |
| **Immutable Artifacts** | Registry enforces tag immutability and scan‑on‑push |

## High‑Level Architecture

```
Developer ─┬─► GitHub Actions  ──► Amazon ECR (immutable registry) ──► ECS Fargate ──► Internet
          │        │                        │                            │
          │        │                        └─► ECR Scan‑on‑Push         │
          │        │                                                     ▼
          │        └─► Trivy CVE Gate (fail on CRITICAL/HIGH)        Automatic Rollback
          └─► GuardDuty (optional runtime telemetry)
```


## Technology Stack

+ **CI Engine**: GitHub Actions (Secrets-based authentication via AWS access keys)
- **Image Build**: Docker Build
- **Vulnerability Scanning**: Aqua Trivy, Amazon ECR Scan‑on‑Push
- **Container Orchestrator**: Amazon ECS on AWS Fargate
- **Infrastructure Security**: IAM least‑privilege roles, ECR lifecycle policies
- **Intrusion Detection**: AWS GuardDuty (runtime monitoring and threat detection)


## Repository Structure

```text
.github/workflows/   # End‑to‑end pipeline (build, scan, deploy, rollback)
app.py               # Sample Flask application (minimal business logic)
Dockerfile           # Production‑ready container definition
requirements.txt     # Python dependencies
docs/                # Screenshots and architecture artefacts
```

## Deployment Workflow (Condensed)

1. Developer pushes code to `main`.
2. GitHub Actions builds the container image and executes a Trivy scan.
3. Image is pushed to Amazon ECR; ECR performs a secondary scan‑on‑push.
4. ECS service is forced to perform a zero‑downtime rolling update.
5. If any step fails, the pipeline reverts to the last stable task definition.

## Evidence Catalogue

Below is the curated evidence package with live GitHub‑hosted screenshots embedded for review.

### 1&nbsp;&nbsp;|&nbsp;Successful CI/CD Execution
![CI Success](https://github.com/user-attachments/assets/53687ba9-3bca-4bb5-a999-53e4daa77550)
<p align="center"><em> End‑to‑end GitHub Actions workflow completing successfully, demonstrating automated build, scan, push, redeploy, and post‑deployment rollback guardrails.</em></p>

---

### 2&nbsp;&nbsp;|&nbsp;Trivy Vulnerability Gate
![Trivy Failure](https://github.com/user-attachments/assets/eb0ef4cf-e073-4dcb-ac37-63e57270eac9)
![Trivy Detected CVEs](https://github.com/user-attachments/assets/d4d67bdd-9bab-4447-a7f9-e7615fea0a32)
<p align="center"><em> Trivy blocks push by detecting CRITICAL/HIGH CVEs (first image) and lists specific vulnerabilities (second image), enforcing shift‑left security.</em></p>

---

### 3&nbsp;&nbsp;|&nbsp;ECR Repository with Immutable Tag
![ECR Repository](https://github.com/user-attachments/assets/27a402e7-e3fd-4afd-a72d-869fe4455390)
<p align="center"><em> Amazon ECR configured with tag immutability and scan‑on‑push, ensuring artefact integrity throughout the supply chain.</em></p>

---

### 4&nbsp;&nbsp;|&nbsp;ECS Service Running Latest Image
![ECS Overview](https://github.com/user-attachments/assets/7619332a-2cd4-42d2-ab4d-0f87b81ef460)
![Public Endpoint](https://github.com/user-attachments/assets/4651c039-5184-4929-9828-76dfe14c70b9)
<p align="center"><em> Service dashboard confirms 1/1 tasks running the latest container image; browser hit to public IP verifies live application response.</em></p>

---

### 5&nbsp;&nbsp;|&nbsp;GuardDuty Runtime Alert
![GuardDuty Alert](https://github.com/user-attachments/assets/43c056e6-0752-4653-9598-4c2bf35c8ce7)
![Alert Details](https://github.com/user-attachments/assets/6523c64d-e344-4b2f-85c9-81a17e833d38)
<p align="center"><em> GuardDuty detects activity from my Kali host, providing source geolocation and context for incident response.</em></p>

---
## Extensibility Roadmap

- Multi‑region blue/green deployments (us‑east‑1, us‑west‑2, eu‑central‑1)
- Slack/SNS notifications for rollback and scan failures
- Runtime anomaly detection via AWS Inspector or Falco
- Policy‑as‑code integration using Open Policy Agent (OPA) or AWS Verified Permissions

## License

Released under the MIT License.
