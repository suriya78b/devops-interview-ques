# 🔁 CI/CD Pipeline & Release Governance
## 1. Describe a strategy to implement multi-approval gated deployments in Azure DevOps or GitHub Actions.
To implement multi-approval gated deployments in Azure DevOps, use environments in YAML pipelines or classic release pipelines with approval gates: Define stages for environments (e.g., staging, prod), add manual approval checks via the Approvals and Checks feature under Project Settings > Environments. Require multiple approvers (e.g., via Azure AD groups) before promoting. In GitHub Actions, use environments in workflows: Configure environment: production in jobs, then set up protection rules in repo settings > Environments with required reviewers (multiple users or teams) and wait timers. This gates deployments, ensuring consensus before proceeding.

## 2. How do you perform policy-as-code validation before every infrastructure deployment?
Perform policy-as-code validation using tools like Open Policy Agent (OPA) or Sentinel (for Terraform). Integrate into CI/CD pipelines: In the build stage, run linters or scanners (e.g., Checkov for IaC, tfsec for Terraform) as a task. For example, in GitHub Actions or Azure Pipelines, add a step to scan code against policies defined in Rego (OPA) or YAML, failing the pipeline if violations occur. Embed policies in repo (e.g., as .rego files) and use webhooks or pre-commit hooks for early feedback.

## 3. How would you isolate staging and production pipelines and secure access in a shared DevOps project?
To isolate staging and production pipelines in a shared DevOps project, use separate environments or branches: In Azure DevOps, create distinct pipelines or stages with branch filters (e.g., main for prod, develop for staging) and secure with variable groups linked to Azure Key Vault. For access, use RBAC: Assign permissions via Azure AD groups (e.g., ProdDeployers for production stages). In GitHub, use branch protection rules and environments with required approvals. Encrypt secrets per environment and use conditional workflows (if: github.ref == 'refs/heads/main') to segregate execution.

## 4. What’s your rollback strategy for stateful applications deployed via Helm or Kustomize?
For stateful apps via Helm or Kustomize, rollback strategy: Use Helm's built-in rollback (helm rollback <release> <revision>) which reverts to previous manifests while preserving PVCs (Persistent Volume Claims) to avoid data loss. For Kustomize (often with ArgoCD or Flux), rely on GitOps: Tag releases, and rollback by reverting Git commits or using ArgoCD's rollback feature, ensuring stateful sets scale down/up gracefully. Always test in lower envs, monitor post-rollback with tools like Prometheus, and have backups (e.g., Velero for Kubernetes resources).

## 5. Explain your CI/CD pipeline architecture end-to-end. How do you ensure zero-downtime deployments?
End-to-end CI/CD architecture: Source control (Git) triggers CI on push/PR (build/test/lint via Jenkins/Azure Pipelines/GitHub Actions). Artifacts stored in repo (e.g., Nexus/Artifactory). CD promotes via gates: Deploy to dev/staging with automated tests, then manual approval for prod. For zero-downtime: Use rolling updates in Kubernetes (maxUnavailable=0), blue-green (switch traffic via ingress), or canary (gradual rollout with monitoring). Ensure health checks, auto-scaling, and feature flags (e.g., LaunchDarkly) for safe reversals.

## 6. How do you design multi-branch workflows for microservices in CI/CD?
For microservices, design multi-branch workflows: Use trunk-based development with short-lived feature branches. CI runs on all branches (unit/integration tests). For releases, main branch for prod, release/* for hotfixes. In pipelines, use matrix strategies (e.g., in GitHub Actions) to build/deploy services in parallel. Monorepo: Selective triggers (if changed paths). Polyrepo: Independent pipelines per service, with shared libraries. Use semantic versioning and contract testing (e.g., Pact) to handle inter-service dependencies.

## 7. Blue-Green vs Rolling vs Canary - when to choose what and why?
Blue-Green: Two identical envs; deploy to inactive (green), switch traffic instantly. Choose for zero-downtime with easy rollback (switch back), but high infra cost—ideal for critical apps. Rolling: Gradual pod replacement. Choose for resource efficiency, but potential mixed versions—good for non-critical, large clusters. Canary: Deploy to subset of users/traffic (e.g., 5%), monitor, then full rollout. Choose for risk mitigation with real-user feedback—best for web apps with A/B testing, but needs advanced routing (e.g., Istio).

## 8. How do you secure CI/CD pipelines from credential leaks & supply-chain attacks?
Secure pipelines: Use secret scanners (e.g., Trivy, GitGuardian) in CI to detect leaks. Store creds in vaults (HashiCorp Vault, Azure Key Vault) with short-lived tokens. For supply-chain: Sign artifacts (cosign), use SBOMs (CycloneDX), scan dependencies (Dependabot, Snyk). Implement least-privilege (RBAC in Jenkins/GitHub), require PR approvals, and use immutable infra. Monitor with audit logs and tools like Falco for runtime threats.

## 9. How do you troubleshoot and reduce flaky pipeline failures?
Troubleshoot flaky failures: Reproduce locally, add retries (e.g., in Jenkins: retry(3) { ... }). Use parallel testing to isolate. Log extensively, monitor metrics (Prometheus). Reduce by: Stabilize envs (use containers), mock externals (WireMock), seed data consistently. Analyze patterns via CI analytics (e.g., Jenkins Blue Ocean), and implement circuit breakers for unstable steps.

## 10. The Jenkins pipeline takes several minutes to perform Git checkout. How do you improve checkout performance?
Improve Jenkins Git checkout: Use shallow clones (e.g., depth=1 in pipeline: git { extensions { cloneOption { depth 1; shallow true } } }). Enable reference repos for large monorepos. Optimize agent resources (more CPU/RAM). Use Git LFS for binaries if applicable. Parallelize non-dependent steps post-checkout.

## 11. The Jenkins server has no internet access. Plugins must be installed offline. How do you handle plugin dependencies?
For offline Jenkins: Download .hpi files and dependencies from update-center.jenkins.io or mirrors using an online machine. Use the Plugin Installation Manager Tool (jenkins-plugin-manager) to resolve deps offline. Upload via Manage Jenkins > Manage Plugins > Advanced > Upload Plugin. For deps, manually fetch transitive ones listed in plugin manifests.

## 12. Deployment works in UAT but fails in production. How do you identify configuration drift?
Identify config drift: Use tools like Driftctl (for IaC) or kube-score (Kubernetes) to compare desired (Git) vs actual state. Run config audits in pipelines. Compare env vars, secrets, and resources via scripts (e.g., terraform plan -detailed-exitcode). Check logs for env-specific diffs, use monitoring (Datadog) for runtime discrepancies.

## 13. Pipeline fails with a generic “permission denied” error. What is your step-by-step RCA approach?
RCA for "permission denied": 1) Check error context (which step/file?). 2) Verify user/context (Jenkins agent running as?). 3) Inspect permissions (ls -l or getfacl on target). 4) Review pipeline config (become/sudo used?). 5) Test manually on agent. 6) Check SELinux/AppArmor if enforced. 7) Audit logs for denials. 8) Reproduce in isolated env.

## 14. How would you design CI/CD for 1000+ parallel deployments safely?
For 1000+ parallel deployments: Use orchestration like ArgoCD/Flux for GitOps, with sync waves and hooks. Scale CI/CD runners (Kubernetes-based agents in Jenkins/Actions). Implement rate limiting, queuing (e.g., semaphore in pipelines). Monitor resource usage, use canaries for safety, and auto-scale infra.

## 15. What are the main stages in a CI/CD pipeline?
Main stages: CI - Checkout, Build, Test (unit/integration), Lint/Scan. CD - Package/Artifact, Deploy (to envs), Verify (smoke/e2e tests), Monitor/Release.

## 16. What is Jenkins, how does it work, and what are the main stages in a pipeline?
Jenkins is an open-source automation server for CI/CD. It works via pipelines (code as Groovy scripts) running on master/agents. Main stages: Build (compile), Test (run tests), Deploy (push to env), Post-actions (notifications). Configured via Jenkinsfile in repo.

## 17. How do you securely store credentials in pipelines?
Securely store creds: Use Jenkins Credential Store (encrypted), integrated with vaults (HashiCorp Vault plugin). In pipelines, inject via withCredentials { }. Avoid hardcoding; use env vars or secret files. Rotate regularly.

## 18. How do you design a production-ready CI/CD pipeline?
Production-ready pipeline: GitOps-driven, with automated tests, security scans, approvals. Multi-stage (CI/CD), immutable artifacts, monitoring (Prometheus), logging (ELK). Zero-downtime deploys, rollbacks, and scalability for high load.

## 19. How do you implement rollback strategies in deployment?
Implement rollbacks: Version artifacts, use Helm rollback or kubectl rollout undo. Automate via failure triggers in pipelines. For apps, feature flags for quick toggles. Test rollbacks in staging.

## 20. How to design a multi-environment CI/CD pipeline?
Multi-env pipeline: Branch-triggered (feature->dev, main->staging/prod). Use variables/env-specific configs (e.g., YAML anchors). Gates between envs, promote artifacts unchanged.

## 21. Scripted vs Declarative pipeline — when do you choose which?
Scripted: Groovy code for complex logic/conditionals—choose for custom flows. Declarative: Structured YAML-like syntax—choose for simplicity, readability in standard pipelines.

## 22. How do you secure Jenkins credentials and users?
Secure Jenkins: Use Matrix-based auth or Role-Based plugin for users. Store creds encrypted, enable CSRF, use HTTPS. Audit plugins, limit agent access.

## 23. How do you trigger Jenkins automatically using Git webhooks?
Trigger via Git webhooks: Configure webhook in Git repo (e.g., GitHub: Settings > Webhooks > Add webhook to Jenkins URL like https://jenkins/job/build). In Jenkins, enable "GitHub hook trigger for GITScm polling" in job config.

## 24. How do you clean workspace & handle pipeline failures?
Clean workspace: Use ws cleanup plugin or deleteDir() in post { always { } }. Handle failures: Use try-catch in scripted, or post { failure { notify } }. Retries, notifications (email/Slack), and archive logs for debugging.
