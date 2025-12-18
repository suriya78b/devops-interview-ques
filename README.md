# DevOps Engineer Interview Questions

# 𝐂𝐨𝐫𝐞 𝐃𝐞𝐯𝐎𝐩𝐬 & 𝐅𝐮𝐧𝐝𝐚𝐦𝐞𝐧𝐭𝐚𝐥𝐬
1. What is DevOps and how is it different from traditional IT operations or Agile?
2. Explain CI vs CD - what problems do they solve in real projects?
3. What are the essential components of a modern DevOps pipeline?
4. Explain IaC (Infrastructure as Code) and why teams adopt it.
5. What is immutable vs mutable infrastructure?

# Cloud Architecture & Compliance (Azure/AWS)
1. How would you design an automated backup and restore mechanism for a core banking system hosted in Azure?
2. What is your approach to ensuring PCI-DSS and RBI compliance in infrastructure provisioning using Terraform?
3. How do you audit and enforce tagging policies for cost visibility across hundreds of cloud resources?
4. What are Azure Blueprints and how would you use them to enforce security baselines in a banking project?

# 🔁 CI/CD Pipeline & Release Governance
1. Describe a strategy to implement multi-approval gated deployments in Azure DevOps or GitHub Actions.
2. How do you perform policy-as-code validation before every infrastructure deployment?
3. How would you isolate staging and production pipelines and secure access in a shared DevOps project?
4. What’s your rollback strategy for stateful applications deployed via Helm or Kustomize?
5. Explain your CI/CD pipeline architecture end-to-end. How do you ensure zero-downtime deployments?
6. How do you design multi-branch workflows for microservices in CI/CD?
7. Blue-Green vs Rolling vs Canary - when to choose what and why?
8. How do you secure CI/CD pipelines from credential leaks & supply-chain attacks?
9. How do you troubleshoot and reduce flaky pipeline failures?
10. The Jenkins pipeline takes several minutes to perform Git checkout. How do you improve checkout performance?
11. The Jenkins server has no internet access. Plugins must be installed offline. How do you handle plugin dependencies?
12. Deployment works in UAT but fails in production. How do you identify configuration drift?
13. Pipeline fails with a generic “permission denied” error. What is your step-by-step RCA approach?
14. How would you design CI/CD for 1000+ parallel deployments safely?
15. What are the main stages in a CI/CD pipeline?
16. What is Jenkins, how does it work, and what are the main stages in a pipeline?
17. How do you securely store credentials in pipelines?
18. How do you design a production-ready CI/CD pipeline?
19. How do you implement rollback strategies in deployment?
20. How to design a multi-environment CI/CD pipeline?
21. Scripted vs Declarative pipeline — when do you choose which?
22. How do you secure Jenkins credentials and users?
23. How do you trigger Jenkins automatically using Git webhooks?
24. How do you clean workspace & handle pipeline failures?

# 𝐃𝐨𝐜𝐤𝐞𝐫 & 𝐂𝐨𝐧𝐭𝐚𝐢𝐧𝐞𝐫𝐬
1. What is Docker?
2. How do you reduce Docker image size and improve build time?
3. What are multi-stage builds and when do you use them?
4. How do you troubleshoot container crashes (OOM, CPU spike, segfault)?
5. What are Docker namespaces and cgroups?
6. Explain multi-stage Docker builds and why they matter.
7. How do you reduce Docker image size?
8. What happens internally when a Docker container starts?
9. Explain difference between ENTRYPOINT and CMD.
10. Explain Docker multi-stage builds with an example.
11. How do you troubleshoot high CPU usage in a container?
12. Best practices for production-ready Dockerfiles?
13. How to handle environment variables securely?
14. What happens internally when you run docker run?

# 🐳 Kubernetes (AKS/EKS) & Container Security
1. How do you secure inter-pod communication in AKS using network policies and service mesh (e.g., Istio)?
2. What steps would you take to ensure container images are free from known vulnerabilities before they reach production?
3. How do you scale AKS workloads based on message queue depth or financial transaction volume?
4. Explain how you’d implement runtime security for containers using tools like Falco or eBPF.
5. Explain your production-grade Kubernetes architecture.
6. How do you troubleshoot CrashLoopBackOff and ImagePullBackOff?
7. What happens when readiness/liveness probes fail?
8. How do you implement autoscaling (HPA/VPA/Cluster Autoscaler)?
9. How do you manage secrets securely in Kubernetes (Vault / Sealed Secrets)?
10. Pods are getting evicted when traffic increases. How do you identify and resolve the eviction reason?
11. New pods stay in “Pending” but the cluster autoscaler is not creating new nodes. What could be wrong?
12. A pod is running but refuses TCP connections – how do you debug?
13. How do you handle GPU scheduling in EKS (or GKE)?
14. How do you implement secure multi-tenant namespaces in Kubernetes?
15. What are the scale limits of kube-proxy iptables, and how do you fix them?
16. How do you detect and resolve half-open TCP connections from a service mesh?
17. What is the use of Helm in Kubernetes?
18. Explain how Kubernetes schedules a pod from request → running state.
19. What are Deployments, StatefulSets, and DaemonSets?
20. What are readiness vs liveness probes and why are they important?
21. How do you implement secrets securely in Kubernetes?
22. How would you troubleshoot CrashLoopBackOff or ImagePullBackOff?
23. Pod in CrashLoopBackOff — how do you debug?
24. Deployment vs StatefulSet vs DaemonSet — real use cases?
25. How do you expose a service externally?
26. How do ConfigMaps and Secrets work?
27. RollingUpdate vs Recreate strategy — when to use which?
 
# 𝐓𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦 / 𝐈𝐧𝐟𝐫𝐚𝐬𝐭𝐫𝐮𝐜𝐭𝐮𝐫𝐞-𝐚𝐬-𝐂𝐨𝐝𝐞
1. What is Terraform and how do you use it?
2. How do you structure Terraform for multi-environment deployments (dev/stage/prod)?
3. What happens internally during terraform plan & apply?
4. How do you resolve state locking issues or partial apply failures?
5. How do you detect and fix drift in Terraform-managed infrastructure?
6. How do you design reusable Terraform modules for microservices?
7. During a production deployment, you get a state lock in the S3 backend because another engineer stopped their apply midway. How do you safely remove the lock?
8. You need to update an autoscaling group in production with zero downtime. How would you design this using Terraform?
9. How do you manage Terraform state in teams?
10. What happens internally during terraform apply?
11. Explain Terraform modules with a scenario.
12. How to prevent accidental deletion of resources?
13. How do you fix drift when changes are done manually on AWS?

# Ansible
1. A user-creation playbook always shows “changed” and is not idempotent. How do you fix it?
2. The AWS dynamic inventory is showing outdated EC2 host details. How do you troubleshoot this?
3. 

# Linux
1. A Java process suddenly uses 100% CPU. What steps and commands do you use to find the cause?
2. The server shows 100% disk usage even after deleting large log files. Why does this happen and how do you actually free the space?
3. How do you print the current date and time in a shell script?
4. How can you view the contents of a ZIP file without extracting it?
5. What does the Linux permission code 755 represent?
6. Which command is used to change directory/file permissions?
7. Which command is used to create a new user in Linux?
8. How do you check all active network ports on a Linux machine?
9. Linux is CLI based — which shell type does it commonly use?
10. Where should environment variables be stored in Linux?
11. How do you debug high CPU / memory consumption in Linux?
12. What is the difference between a process, thread, and service?
13. What is SSH key-based authentication and why is it recommended?



# Networking
1. A microservice frequently experiences packet drops. How do you debug using tcpdump and traceroute?
2. DNS resolution inside Kubernetes takes 2–3 seconds. How do you troubleshoot slow DNS?
3. What is SMTP, its uses, and its types?
4. DNS uses which protocols — TCP, UDP, or HTTPS?
5. What are common HTTPS API methods?
6. Explain firewall concepts, inbound vs outbound rules, and ports.

# 𝐀𝐖𝐒 𝐂𝐥𝐨𝐮𝐝, 𝐍𝐞𝐭𝐰𝐨𝐫𝐤𝐢𝐧𝐠 & 𝐑𝐞𝐥𝐢𝐚𝐛𝐢𝐥𝐢𝐭𝐲
1. Explain your VPC design across AZs and NAT/IGW routing.
2. How do you troubleshoot sudden spikes in 5xx errors in production?
3. How do you implement centralized logging using CloudWatch / ELK / OpenSearch?
4. How do you troubleshoot latency spikes in South America?
5. How do you investigate a NAT Gateway cost spike?
6. What is the difference between an ALB and NLB?
7. What is Route 53 and why do we use it?
8. What is a Target Group in AWS?
9. Difference between GP2 and GP3 EBS volumes?
10. What are T-Series EC2 instances and why are they used?
11. What is EFS, how do you mount it, and why is it used?
12. What is an Internet Gateway (IGW)?
13. How do we restrict access from a specific IP/location?
14. Difference between Security Group and NACL?
15. How do you recover an EC2 instance if the key pair is lost?
16. How will you auto-scale an application when traffic spikes?
17. Difference between ALB, NLB & CLB — where to use which?
18. How will you secure an S3 bucket for both public and private access?
19. Your EC2 becomes unreachable — how will you troubleshoot?
20. How do you implement Blue-Green deployment using AWS services?
     
# 𝐆𝐢𝐭, 𝐁𝐫𝐚𝐧𝐜𝐡𝐢𝐧𝐠 & 𝐕𝐞𝐫𝐬𝐢𝐨𝐧 𝐂𝐨𝐧𝐭𝐫𝐨𝐥
1. Explain GitFlow vs Trunk-Based Development. Which one do product companies prefer & why?
2. How do you debug production issues using Git bisect?
3. How do you enforce code quality checks and approvals before merge?
4. Explain Git branching strategies: GitFlow vs trunk-based development.
12. What is the difference between merge, rebase, and cherry-pick?
13. What are pre-commit hooks and why are they used?


# 🔐 Secrets Management & IAM
1. What is your strategy for auto-rotating secrets across services using Azure Key Vault or AWS Secrets Manager?
2. How do you implement least privilege access controls across DevOps pipelines, clusters, and cloud resources?
3. Have you enforced multi-factor access for infrastructure automation tools (e.g., Terraform Cloud, GitHub runners)?

#📈 Monitoring, Logging & SRE Practices
1. How do you set up SLOs for banking APIs and trigger alerts when they breach thresholds?
2. Which tools do you use for tracing and diagnosing latency in distributed systems (e.g., OpenTelemetry, Jaeger)?
3. How do you differentiate false positives from real incidents in cloud monitoring setups?
4. How do you define SLIs/SLOs and prevent production incidents?
5. Grafana shows latency spikes but CPU/RAM remain normal. How do you isolate the root cause?
6. The team receives too many alerts per day. How do you check alerting to reduce noise?
7.  What are the main components of the ELK Stack?
8.  

Gradle & Maven
1. What are the key architectural differences between Maven and Gradle?
2. How do dependency conflicts occur and how do you resolve them?
3. What is the role of pom.xml vs build.gradle in CI pipelines?
4. How do you optimize build time in large Java projects?
5. How do you integrate Maven/Gradle with CI tools and artifact repositories?

𝐑𝐞𝐚𝐥-𝐰𝐨𝐫𝐥𝐝 & 𝐈𝐧𝐜𝐢𝐝𝐞𝐧𝐭 𝐒𝐜𝐞𝐧𝐚𝐫𝐢𝐨𝐬
26. The system is slow - how do you start debugging?
27. Deployment succeeded but app is down - what’s your checklist?
28. Production certificate expired - what would you do?
29. Kubernetes pod is running but service unreachable - troubleshoot.
30. A rollback is required but DB schema changed - your strategy?

𝐂𝐥𝐨𝐮𝐝 & 𝐂𝐨𝐬𝐭 𝐄𝐟𝐟𝐢𝐜𝐢𝐞𝐧𝐭 𝐀𝐫𝐜𝐡𝐢𝐭𝐞𝐜𝐭𝐮𝐫𝐞
31. Explain horizontal vs vertical scaling with examples.
32. How do you design multi-AZ / multi-region architecture?
33. What are spot, reserved, and on-demand instances?
34. Explain cloud security best practices like IAM least privilege.
35. How do you estimate and optimize cloud cost?


Leadership, Culture & Chaos Influence
 🔥 How would you run a chaos drill during a launch rehearsal?
 ⚖️ How do you balance cost efficiency vs. high availability pre-warming?
 📋 What’s your pre-flight checklist for Netflix-scale infrastructure?
 👀 Why is observability debt equal to tech debt, and how do you address it?
