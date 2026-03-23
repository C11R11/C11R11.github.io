# 🎯 Checklist de Preparación: GitHub Actions Certification (GH-200)

* [Github](cli-github.md) - Github
* [Github actions](github-actions.md) - Github actions
* [GH-200 checklist](github-cert-checklist.md) - GH-200: GitHub Actions Certification Checklist
* [Semantic versioning](semantic-versioning)
* [Branching strategies](github-branching-strategies.md)
* [Full gitops CICD pipeline description](github-gitops.md)

# Notes

* [Domain 1 - Author and Manage Workflows (20–25%)](github-cert-domain1.md)
* [Domain 2 - Consume and Troubleshoot Workflows (15–20%)](github-cert-domain2.md)
* [Domain 3 - Author and Maintain Actions (15–20%)](github-cert-domain3.md)
* [Domain 4 - Manage GitHub Actions for the Enterprise (20–25%)](github-cert-domain4.md)
* [Domain 5 - Secure and Optimize Automation (10–15%)](github-cert-domain5.md)

# 🎓 GitHub Actions (GH-200) Certification Domains (2026)

The exam is designed for DevOps engineers and software developers with intermediate experience. It evaluates five key functional areas:

## 1. Author and Manage Workflows (20–25%)
* **Syntax & Structure:** Mastery of YAML syntax, including `jobs`, `steps`, and conditional logic (`if`).
* **Triggers:** Configuring workflows for pushes, pull requests, manual triggers (`workflow_dispatch`), and complex webhook events.
* **Complex Orchestration:** Using job dependencies (`needs`), matrices (`strategy`), and service containers (`services`).
* **Environment Variables:** Managing default and custom variables at various scopes.

## 2. Consume and Troubleshoot Workflows (15–20%)
* **Observability:** Locating and interpreting workflow run logs, status badges, and deployment history.
* **Debugging:** Enabling step-debug logging and diagnosing failures in composite or multi-job workflows.
* **Data Management:** Passing data between jobs using `outputs` and managing workflow `artifacts`.

## 3. Author and Maintain Actions (15–20%)
* **Action Types:** Creating and troubleshooting JavaScript, Docker, and Composite Actions.
* **Metadata & Structure:** Defining `action.yml`, directory structures, and required inputs/outputs.
* **Versioning:** Implementing versioning and release strategies (Tags vs. SHAs) for internal and public distribution.

## 4. Manage GitHub Actions for the Enterprise (20–25%)
* **Runners:** Configuring GitHub-hosted and self-hosted runners, including runner groups and labels.
* **Governance:** Implementing organizational use policies, controlling access to actions, and using reusable workflow templates.
* **Scaling:** Managing concurrency and large-scale runner infrastructure (e.g., Actions Runner Controller).

## 5. Secure and Optimize Automation (10–15%)
* **Secret Management:** Scoping and accessing encrypted secrets at the Org, Repo, and Environment levels.
* **Hardening:** Mitigating script injection, using OIDC for cloud authentication, and applying the principle of least privilege.
* **Efficiency:** Configuring caching strategies and retention policies to optimize performance and reduce costs.

# 📚 Recommended Resources: DevOps Governance & Security

The following resources cover the intersection of automation, supply chain security, and organizational governance—essential for the "Release Manager" role.

## 📖 Foundational Philosophy
* **The Phoenix Project / The DevOps Handbook** * *Authors:* Gene Kim, Jez Humble, et al.  
  * *Focus:* The definitive guides on how DevOps and Security collaborate to create a "Software Supply Chain."

## 🛡️ Security Frameworks
* **SLSA (Supply Chain Levels for Software Artifacts)** * *Source:* [slsa.dev](https://slsa.dev)  
  * *Focus:* An industry-standard framework for ensuring artifacts (like your versioning scripts) are secure and identifiable via SHAs.
* **OpenSSF Scorecard** * *Source:* [securityscorecards.dev](https://securityscorecards.dev/)  
  * *Focus:* An automated tool that audits your repositories and provides a security score based on practices like SHA pinning.

## 📺 Technical Deep Dives
* **GitHub Actions: The Missing Guide** * *Creators:* Look for Brian Demers or Arie Leeuwesteijn on YouTube.  
  * *Focus:* Advanced scaling of Reusable Workflows vs. Composite Actions across an organization.
* **GitHub Official Security Documentation** * *Focus:* Deep dives into Repository and Organization-level Action policies for enforcing immutable references.