# Container Image Scanning Pipeline with Trivy and GitHub Actions

This project builds a container image for a small Python application and scans it with Trivy during the CI process. The workflow generates a vulnerability report and enforces a security gate by failing the pipeline when High and Critical findings exceed a defined limit. An HTML report is uploaded as an artifact to support review and documentation.

![Container Scanning Diagram](/assets/images/devsecops/container_image-scanning_trivy.png)

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

## Future Enhancements
- Add automated remediation suggestions
- Integrate policy-as-code tooling
- Include signature verification with cosign