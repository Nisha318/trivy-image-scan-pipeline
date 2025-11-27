# Container Image Scanning Pipeline with Trivy and GitHub Actions
![CI Status](https://github.com/Nisha318/trivy-image-scan-pipeline/actions/workflows/trivy-image-scan.yml/badge.svg)
![SBOM](https://img.shields.io/badge/SBOM-Syft-blue)
![SAST](https://img.shields.io/badge/SAST-Semgrep-green)
![Container Security](https://img.shields.io/badge/Trivy-Scanning-orange)

This project demonstrates a security-focused CI workflow that scans container images during the build process using Trivy, enforces a vulnerability threshold, and generates reviewable artifacts such as HTML reports, SBOMs, and SAST findings. It shows how automated controls can identify risks early, prevent unsafe images from progressing, and increase visibility into the software supply chain.

![Container Scanning Banner](/assets/images/devsecops/container_image-scanning_trivy.png)

## Table of Contents
- [Overview](#overview)
- [Features](#features)
- [Repository Structure](#repository-structure)
- [How the Workflow Operates](#how-the-workflow-operates)
- [CI Workflow Diagram](#ci-workflow-diagram)
- [Testing the Vulnerability Gate](#testing-the-vulnerability-gate)
- [Before and After Examples](#before-and-after-examples)
- [Optional: SBOM and SAST Workflows](#optional-sbom-and-sast-workflows)
- [Why This Project Matters](#why-this-project-matters)
- [RMF Control Mapping](#rmf-control-mapping)
- [Evidence Folder](#evidence-folder)
- [Future Enhancements](#future-enhancements)

## Overview

This repository demonstrates a DevSecOps-aligned CI workflow that scans container images for vulnerabilities, enforces security thresholds, and produces artifacts such as HTML reports, SBOMs, and SARIF static analysis results.

## Features

- Automated Docker image build  
- Trivy scan integrated directly into GitHub Actions  
- Threshold gate for High and Critical vulnerabilities  
- Sample containerized application included for testing and demonstration  
- Optional workflows for SBOM and static analysis  


## Repository Structure

```text
app/
  app.py
  requirements.txt
Dockerfile
.github/
  workflows/
    trivy-image-scan.yml
    sbom.yml             (optional)
    sast.yml             (optional)
reports/
  sample-report.html     (optional)
README.md
```

## How the Workflow Operates

1. Build Phase  
   GitHub Actions checks out the repository and builds the Docker image.

2. Scan Phase  
   Trivy scans the image and produces a JSON report.

3. Gating Phase  
   The workflow counts High and Critical findings and fails if the threshold is exceeded.


The pipeline fails if the count exceeds the configured threshold.

The JSON report is converted to HTML and uploaded as an artifact.

This represents a simple CI control that prevents vulnerable images from advancing through the build pipeline.

## CI Workflow Diagram
```mermaid
flowchart TD

    A[Code Push or Pull Request] --> B[GitHub Actions Workflow Starts]

    B --> C[Build Docker Image]
    C --> D[Trivy Image Scan (JSON)]

    D --> E{High + Critical > Threshold?}
    E -->|Yes| F[Fail Pipeline]
    E -->|No| G[Pass Pipeline]

    D --> H[Generate HTML Report]
    H --> I[Upload Report Artifact]

    B --> J[Syft SBOM Generation]
    B --> K[Semgrep SAST Scan]

    J --> I
    K --> I

    G --> L[Ready for Deployment]
    ```

## Testing the Vulnerability Gate

This project uses a configurable severity threshold that affects the CI result.

### Passing Example

- Threshold set to five
- Image contains only Medium or Low findings
- Pipeline passes
- HTML report available for review

### Failing Example

- Threshold set to zero
- A High or Critical finding triggers failure
- Pipeline stops
- HTML report highlights the finding

These scenarios demonstrate how the severity gate is used to enforce security expectations.

## Before and After Examples

### Before: Passing Build

- No High or Critical vulnerabilities
- Threshold not exceeded
- Job completes successfully
- HTML report provided as an artifact

### After: Failing Build

- Threshold set to zero
- Trivy detects a vulnerability
- Job fails during gating
- HTML report shows the triggering finding

These examples show how the gate helps prevent unsafe images from proceeding further into a CI/CD pipeline.

## Optional: SBOM and SAST Workflows

In addition to image scanning, this repository includes optional workflows for SBOM generation and static analysis. These jobs produce artifacts that support supply chain visibility and code review, without blocking the main build.

### SBOM Generation (Syft)

The SBOM workflow uses Syft to generate an SPDX JSON SBOM from the repository source and uploads it as an artifact. This job provides an inventory of components that can be used for vulnerability management and compliance documentation.

### SAST Scan (Semgrep)

The SAST workflow runs Semgrep against the Python application and saves a SARIF report. The job is configured as non-blocking so that findings are recorded but do not fail the pipeline.  This job demonstrates static analysis integration in CI and produces artifacts that can be viewed in supporting tools or used during reviews.

## Why This Project Matters

This workflow supports:

- Early detection of known vulnerabilities
- Enforcement of risk tolerance before deployment
- Improved visibility into image components
- Practical application of supply chain security controls
- Simple integration with CI processes

It reinforces the ability to secure containerized workloads using common DevSecOps practices.

## RMF Control Mapping

The security checks performed in this pipeline align with several NIST SP 800-53 controls that address supply chain security, vulnerability management, and continuous monitoring.

| Control | Control Name | How This Project Supports It |
|--------|--------------|------------------------------|
| RA-5 | Vulnerability Monitoring and Scanning | Trivy performs automated vulnerability scans of container images during CI, identifying known CVEs before deployment. |
| SI-2 | Flaw Remediation | The gating logic prevents images with excessive High or Critical vulnerabilities from passing, forcing remediation before promotion. |
| SA-11 | Developer Testing and Evaluation | SBOM generation and SAST scanning support secure development practices and evaluation during the build process. |
| SA-11(1) | Static Code Analysis | Semgrep performs static analysis to detect insecure coding patterns within the application code. |
| SA-15 | Development Process, Standards, and Tools | By integrating scanning tools into CI/CD, this project demonstrates secure development toolchain controls. |
| PM-30 | Supply Chain Risk Management | SBOM creation and artifact capture increase transparency into dependencies and image contents. |
| SI-7 | Software, Firmware, and Information Integrity | The workflow detects unauthorized or vulnerable components within the image, protecting integrity. |
| CM-2 | Baseline Configuration | The Dockerfile and container build steps enforce a consistent, repeatable baseline configuration. |
| CM-3 | Configuration Change Control | Every image build is versioned through GitHub Actions, allowing traceability of changes. |
| CA-7 | Continuous Monitoring | Pipeline-based scanning represents ongoing automated monitoring of security posture. |


## Future Enhancements
- Add automated remediation suggestions
- Integrate policy-as-code tooling
- Include signature verification with cosign