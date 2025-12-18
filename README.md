# DevOps Engineer Interview Questions

Cloud Architecture & Compliance (Azure/AWS)
1. How would you design an automated backup and restore mechanism for a core banking system hosted in Azure?
2. What is your approach to ensuring PCI-DSS and RBI compliance in infrastructure provisioning using Terraform?
3. How do you audit and enforce tagging policies for cost visibility across hundreds of cloud resources?
4. What are Azure Blueprints and how would you use them to enforce security baselines in a banking project?

#🔁 CI/CD Pipeline & Release Governance
1. Describe a strategy to implement multi-approval gated deployments in Azure DevOps or GitHub Actions.
2. How do you perform policy-as-code validation before every infrastructure deployment?
3. How would you isolate staging and production pipelines and secure access in a shared DevOps project?
4. What’s your rollback strategy for stateful applications deployed via Helm or Kustomize?
5. Explain your CI/CD pipeline architecture end-to-end. How do you ensure zero-downtime deployments?
6. How do you design multi-branch workflows for microservices in CI/CD?
7. Blue-Green vs Rolling vs Canary - when to choose what and why?
8. How do you secure CI/CD pipelines from credential leaks & supply-chain attacks?
9. How do you troubleshoot and reduce flaky pipeline failures?

𝐃𝐨𝐜𝐤𝐞𝐫 & 𝐂𝐨𝐧𝐭𝐚𝐢𝐧𝐞𝐫𝐬
1. How do you reduce Docker image size and improve build time?
2. What are multi-stage builds and when do you use them?
3. How do you troubleshoot container crashes (OOM, CPU spike, segfault)?

#🐳 Kubernetes (AKS/EKS) & Container Security
1. How do you secure inter-pod communication in AKS using network policies and service mesh (e.g., Istio)?
2. What steps would you take to ensure container images are free from known vulnerabilities before they reach production?
3. How do you scale AKS workloads based on message queue depth or financial transaction volume?
4. Explain how you’d implement runtime security for containers using tools like Falco or eBPF.
5. Explain your production-grade Kubernetes architecture.
6. How do you troubleshoot CrashLoopBackOff and ImagePullBackOff?
7. What happens when readiness/liveness probes fail?
8. How do you implement autoscaling (HPA/VPA/Cluster Autoscaler)?
9. How do you manage secrets securely in Kubernetes (Vault / Sealed Secrets)?
 
𝐓𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦 / 𝐈𝐧𝐟𝐫𝐚𝐬𝐭𝐫𝐮𝐜𝐭𝐮𝐫𝐞-𝐚𝐬-𝐂𝐨𝐝𝐞
1. How do you structure Terraform for multi-environment deployments (dev/stage/prod)?
2. What happens internally during terraform plan & apply?
3. How do you resolve state locking issues or partial apply failures?
4. How do you detect and fix drift in Terraform-managed infrastructure?
5. How do you design reusable Terraform modules for microservices?

𝐀𝐖𝐒 𝐂𝐥𝐨𝐮𝐝, 𝐍𝐞𝐭𝐰𝐨𝐫𝐤𝐢𝐧𝐠 & 𝐑𝐞𝐥𝐢𝐚𝐛𝐢𝐥𝐢𝐭𝐲
1. Explain your VPC design across AZs and NAT/IGW routing.
2. How do you troubleshoot sudden spikes in 5xx errors in production?
3. How do you implement centralized logging using CloudWatch / ELK / OpenSearch?

𝐆𝐢𝐭, 𝐁𝐫𝐚𝐧𝐜𝐡𝐢𝐧𝐠 & 𝐕𝐞𝐫𝐬𝐢𝐨𝐧 𝐂𝐨𝐧𝐭𝐫𝐨𝐥
1. Explain GitFlow vs Trunk-Based Development. Which one do product companies prefer & why?
2. How do you debug production issues using Git bisect?
3. How do you enforce code quality checks and approvals before merge?

#🔐 Secrets Management & IAM
1. What is your strategy for auto-rotating secrets across services using Azure Key Vault or AWS Secrets Manager?
2. How do you implement least privilege access controls across DevOps pipelines, clusters, and cloud resources?
3. Have you enforced multi-factor access for infrastructure automation tools (e.g., Terraform Cloud, GitHub runners)?

#📈 Monitoring, Logging & SRE Practices
1. How do you set up SLOs for banking APIs and trigger alerts when they breach thresholds?
2. Which tools do you use for tracing and diagnosing latency in distributed systems (e.g., OpenTelemetry, Jaeger)?
3. How do you differentiate false positives from real incidents in cloud monitoring setups?
4. How do you define SLIs/SLOs and prevent production incidents?


Gradle & Maven
1. What are the key architectural differences between Maven and Gradle?
2. How do dependency conflicts occur and how do you resolve them?
3. What is the role of pom.xml vs build.gradle in CI pipelines?
4. How do you optimize build time in large Java projects?
5. How do you integrate Maven/Gradle with CI tools and artifact repositories?
