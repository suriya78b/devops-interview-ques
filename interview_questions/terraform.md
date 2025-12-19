# 𝐓𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦 / 𝐈𝐧𝐟𝐫𝐚𝐬𝐭𝐫𝐮𝐜𝐭𝐮𝐫𝐞-𝐚𝐬-𝐂𝐨𝐝𝐞
## 1. What is Terraform and how do you use it?

Terraform is an open-source Infrastructure as Code (IaC) tool by HashiCorp that provisions and manages infrastructure across clouds (AWS, Azure, GCP) and on-premises using declarative configuration files. It uses a desired-state model: Describe what you want in code, and Terraform makes it reality.
Key Concepts:

Providers: Plugins for services (e.g., provider "aws" { region = "us-east-1" }).
Resources: Infrastructure objects (e.g., resource "aws_instance" "web" { ami = "ami-123" }).
Data Sources: Query existing resources (e.g., data "aws_ami" "latest" { ... }).
State: Tracks real-world state (stored in terraform.tfstate).

How to Use It:

Install: Download from terraform.io; add to PATH. Verify: terraform version.
Init: terraform init – Downloads providers/modules, sets up backend.
Plan: terraform plan – Previews changes without applying.
Apply: terraform apply – Creates/updates resources (type "yes" to confirm).
Destroy: terraform destroy – Tears down infrastructure.

Workflow: Write .tf files → Init → Plan → Apply. Use variables (variables.tf) for reusability and outputs (outputs.tf) for values. Best for multi-cloud consistency; integrates with CI/CD (e.g., GitHub Actions).

## 2. How do you structure Terraform for multi-environment deployments (dev/stage/prod)?

Multi-environment structure avoids code duplication while isolating changes. Use a monorepo with environment-specific vars or separate directories.
Recommended Structure (Monorepo):
```
terraform/
├── environments/
│   ├── dev/
│   │   ├── terraform.tfvars  # dev.tfvars: instance_count = 1
│   │   └── backend.tf        # S3 bucket for dev state
│   ├── stage/
│   │   └── terraform.tfvars  # stage.tfvars: instance_count = 3
│   └── prod/
│       └── terraform.tfvars  # prod.tfvars: instance_count = 5
├── modules/                  # Reusable modules (e.g., vpc/)
│   └── vpc/
│       ├── main.tf
│       ├── variables.tf
│       └── outputs.tf
├── main.tf                   # Calls modules with vars
├── variables.tf              # Common vars
└── backend.tf                # Shared backend config (commented per env)
```
Steps:

Workspaces: terraform workspace new dev (isolates state; less common now).
Var Files: Load env-specific: terraform apply -var-file=environments/dev/terraform.tfvars.
Backends: Per-env S3 buckets (e.g., s3_bucket = "tf-state-dev" in backend.tf).
CI/CD: Use Terragrunt for DRY (Don't Repeat Yourself) wrappers, or Atlantis for PR-based applies.

Benefits: Promotes GitOps; use tags/labels for env (e.g., environment = var.env). Avoid hardcoding—use conditionals sparingly.

## 3. What happens internally during terraform plan & apply?

Terraform uses a graph-based execution model with providers for CRUD operations.
Terraform Plan:

Read State: Loads terraform.tfstate to know current infra.
Parse Config: Builds resource graph from .tf files (dependencies via depends_on).
Refresh: Queries providers for real-state (updates state if drifted).
Diff: Compares desired (config) vs. actual (state/provider); computes plan (add/change/destroy).
Output: JSON plan file (tfplan) with no-op for unchanged. Command: terraform plan -out=tfplan.

No changes made—safe preview.
Terraform Apply:

Load Plan: If using -out, loads saved plan; else, runs plan inline.
Graph Walk: Executes in topological order (parallel where possible via -parallelism).
Provider Calls: For each resource: Create/Update/Destroy via API (e.g., AWS SDK).
State Update: Writes new state atomically (via backend lock).
Outputs: Displays changes, costs (if enabled).

Hooks: Use provisioners for post-actions (e.g., ansible-pull). Errors rollback partially; use targeted applies (-target=resource).

## 4. How do you resolve state locking issues or partial apply failures?

State locking prevents concurrent modifies; backends like S3 use DynamoDB.
State Locking Issues:

Cause: Another process holds lock (e.g., hung apply).
Resolve:
Check: terraform force-unlock <lock-id> (get ID from error).
Verify: No active sessions (e.g., AWS Console DynamoDB).
Backend-Specific: For S3, delete lock entry in DynamoDB if confirmed stale.


Partial Apply Failures:

Cause: Mid-apply error (e.g., API quota); state inconsistent.
Resolve:
Taint: terraform taint aws_instance.web – Marks for recreation.
Import: terraform import aws_instance.web i-123 – Pulls existing into state.
Plan Again: terraform plan shows diffs; apply targeted.
State Manip: terraform state rm/move/list for fixes (backup first!).


Best: Use remote backends, CI/CD retries, and terraform apply -auto-approve cautiously. Monitor with tfsec or Checkov.

5. How do you detect and fix drift in Terraform-managed infrastructure?

Drift occurs when real infra changes outside Terraform (e.g., console edits).
Detection:

Refresh: terraform refresh – Updates state from providers without changes.
Plan: terraform plan – Shows diffs (e.g., "attribute changed").
Automated: Use Atlantis or Spacelift for scheduled plans; integrate with Driftctl or tfdrift tools: driftctl scan --from tfstate://.
Outputs: Compare terraform output vs. expected.

Fix:

Reconcile: terraform apply – Aligns to config (overwrites drift).
Import: For new/unknown resources: terraform import.
Ignore: Use lifecycle { ignore_changes = [tags] } in resource block for tolerated drift.
Prevent: RBAC (least-privilege IAM), policy-as-code (OPA), alerts on console access.

Run daily drifts in CI; document tolerances.

## 6. How do you design reusable Terraform modules for microservices?

Modules encapsulate reusable code (e.g., ECS for microservices).
Design Principles:

Single Responsibility: One module per concern (e.g., ecs-cluster vs. monolithic).
Inputs/Outputs: Comprehensive variables.tf (defaults, validation: validation { condition = var.port > 0 }) and outputs.tf.
Versioning: Tag releases in Git; use source = "git::https://github.com/org/ecs-module?ref=v1.0".

Example Structure for Microservice ECS Module:
textmodules/ecs-service/
├── main.tf          # resource "aws_ecs_service" "app" { ... family = var.service_name }
├── variables.tf     # variable "service_name" { type = string }
│                    # variable "image" { type = string }  # e.g., ECR repo
│                    # variable "cpu" { type = number, default = 256 }
├── outputs.tf       # output "service_arn" { value = aws_ecs_service.app.arn }
└── versions.tf      # terraform { required_providers { aws = { source = "hashicorp/aws", version = "~> 5.0" } } }
Usage:
In root:
hclmodule "payment-service" {
  source      = "./modules/ecs-service"
  service_name = "payments"
  image       = "123.dkr.ecr.us-east-1.amazonaws.com/payments:v1"
  cpu         = 512
}
Test with terraform validate; publish to Terraform Registry for sharing. Use Terragrunt for composition.

## 7. During a production deployment, you get a state lock in the S3 backend because another engineer stopped their apply midway. How do you safely remove the lock?

S3 backend uses DynamoDB for locks (table: terraform-locks).
Steps to Safely Remove:

Confirm Stale: Coordinate with team—check Slack/Jira for active sessions. Use terraform force-unlock only if confirmed (gets lock ID from error: e.g., "Lock ID: abc123").
Command: terraform force-unlock abc123.
Manual Unlock (If Needed):
AWS Console: DynamoDB → Table → Items → Find lock ID → Delete item.
CLI: aws dynamodb delete-item --table-name terraform-locks --key '{"LockID": {"S": "abc123"}}'.

Post-Removal: terraform plan to verify state integrity; apply if needed.
Prevent Recurrence: Enforce timeouts in CI (e.g., 30min apply limit), use workspaces, or tools like Scalr for queuing.

Always backup state (terraform state pull > backup.tfstate) before force actions. Document in runbook.

## 8. You need to update an autoscaling group in production with zero downtime. How would you design this using Terraform?

ASGs support rolling updates; design for blue-green or in-place with min-healthy.
Design:

Resource Config: Use min_healthy_percent and max_unhealthy_percent in aws_autoscaling_group.hclresource "aws_autoscaling_group" "web" {
  name                = "web-asg"
  min_size            = 2
  max_size            = 10
  health_check_type   = "ELB"
  health_check_grace_period = 300
  # Rolling update
  default_cooldown    = 300
  vpc_zone_identifier = var.subnet_ids
  target_group_arns   = [aws_lb_target_group.web.arn]
  launch_template {
    id      = aws_launch_template.web.id
    version = "$Latest"  # Or specific for controlled rollout
  }
}
Update Strategy:
In-Place: Change launch template version: terraform apply rolls instances gradually (ASG handles drain).
Zero-Downtime: Pair with ALB (connection draining); use lifecycle { create_before_destroy = true } for resources.
Advanced: Module with desired_capacity var; CI/CD deploys to canary first.

Validation: terraform plan previews; monitor via CloudWatch. Rollback: Revert template version.

Use for EC2/ECS; test in stage.

## 9. How do you manage Terraform state in teams?

State (tfstate) tracks resources; mismanagement causes conflicts.
Best Practices:

Remote Backend: Always use (e.g., S3 + DynamoDB):
```
terraform {
  backend "s3" {
    bucket         = "my-tf-state"
    key            = "prod/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-locks"
  }
}
```
Init with -migrate-state for switches.
Team Workflow:
Workspaces: terraform workspace select prod – Per-env isolation.
State Splitting: Separate files per component (e.g., network.tfstate, app.tfstate) via -state flag.
Access Control: IAM policies on S3; use Sentinel for policy.
Versioning: Enable S3 versioning; backup with terraform state pull.

Collaboration: PR-based applies (Atlantis), state sharing via outputs. Avoid local state.

## 10. What happens internally during terraform apply?

(See Question 3 for plan details; apply builds on it.)
Summary:

Plan Execution: Loads/re-runs plan if not saved.
Dependency Graph: Builds DAG; walks in order (providers fetch creds).
Resource Actions: For each:
Create: Provider API call (e.g., POST /instances).
Update: PATCH with diffs.
Destroy: DELETE (post-hooks run).

State Sync: Atomic write to backend; partial failures leave state as-is.
Refresh: Implicit post-apply.

Parallelism: Up to 10 ops default. Use -lock=false cautiously.

## 11. Explain Terraform modules with a scenario.

Modules are containers for reusable configs (like functions).
Scenario: Provisioning VPCs for a microservices app.

Base Module (modules/vpc/main.tf):
```
resource "aws_vpc" "main" {
  cidr_block = var.cidr
  tags = { Name = var.env }
}
output "vpc_id" { value = aws_vpc.main.id }
variables.tf: variable "cidr" { type = string }  # e.g., "10.0.0.0/16"
```
Root Usage (main.tf):
```
module "dev_vpc" {
  source = "./modules/vpc"
  cidr   = "10.0.0.0/16"
  env    = "dev"
}
module "prod_vpc" {
  source = "./modules/vpc"
  cidr   = "10.1.0.0/16"
  env    = "prod"
}
```
Benefits: DRY code; test via terraform validate in module dir. Scenario scales to 100s of envs without copy-paste.

## 12. How to prevent accidental deletion of resources?

Use built-in and external guards.

Lifecycle Rules: In resource:
```
lifecycle {
  prevent_destroy = true
}
```
terraform apply fails on destroy attempts.
State Flags: terraform state rm requires explicit; use -force sparingly.
Provider-Level: AWS: Enable termination protection on EC2 (disable_api_termination = true).
External Tools:
OPA/Terraform Sentinel: Policy: main = rule { plan.changes.resources[i].type != "aws_s3_bucket" }.
CI Gates: Pre-apply hooks in GitHub Actions to scan plans.

Tagging: Tag critical resources; filter destroys.

Review plans always; use for prod S3 buckets/databases.

## 13. How do you fix drift when changes are done manually on AWS?

(See Question 5 for general drift.)
AWS-Specific: Console/CLI changes (e.g., added tag to EC2).
Fix Steps:

Detect: terraform plan shows diff (e.g., tags diverged). Use AWS Config rules for alerts.
Import (If New Resource): terraform import aws_instance.web i-123456. Edit config to match.
Reconcile: terraform apply – Overwrites manual changes (e.g., removes extra tags).
Ignore Tolerated: lifecycle { ignore_changes = [tags["Owner"]] }.
Audit: Enable AWS CloudTrail; integrate with Driftbot for scans.

Prevent: Lock down IAM (deny console for prod); enforce via Service Control Policies (SCPs). Re-apply post-incident.
