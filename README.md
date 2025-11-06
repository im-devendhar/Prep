# ***Prep


---

# 💥 **DevOps Interview Q&A – Full Preparation Guide (Concise + Conversational)**

---

## ☁️ **1. AWS (EC2, S3, IAM, Lambda, CloudWatch, SNS)**

**1. Can’t SSH into EC2 — what do you check?**
I’d first verify the EC2’s security group allows inbound SSH (port 22), confirm the key pair is correct, and ensure I’m using the right user (ec2-user/ubuntu). If it’s a private subnet, I’d check connectivity through a bastion host or VPN.

**2. EC2 not accessible via browser — why?**
Usually, the issue is missing HTTP/HTTPS rules in the security group or Nginx/Apache not running. I’d check firewall rules and confirm the web server is listening on port 80 or 443.

**3. Uploaded S3 files not public — what do you check?**
Check bucket policy, object ACL, and Block Public Access settings. Often, “Block all public access” prevents visibility, so I’d modify that or use presigned URLs for secure access.

**4. Restrict S3 access to specific users/roles?**
Use IAM policies and bucket policies together. I’d allow access via roles attached to EC2/Lambda while denying public access.

**5. Stop/start EC2 — does public IP remain?**
No, the public IP changes unless it’s an Elastic IP. I’d assign an Elastic IP for persistent access.

**6. Automate EC2 creation?**
Use Terraform, CloudFormation, or AWS CLI scripts specifying AMI, instance type, tags, and security groups. In CI/CD, Jenkins can trigger this automatically.

**7. Storing logs securely?**
S3 is ideal with versioning and server-side encryption (SSE-S3 or SSE-KMS). I’d restrict access via IAM and enable lifecycle rules to archive old logs.

**8. Lambda needs S3 access — how?**
Attach an IAM role to Lambda with `s3:GetObject` or `PutObject` permissions. Avoid embedding keys in code.

**9. IAM user vs role vs policy?**
User = person/account; Role = temporary permissions (used by EC2/Lambda); Policy = rules that define what’s allowed or denied.

**10. Automate notifications on high CPU?**
Set a CloudWatch alarm for CPU > 80% and trigger an SNS topic to send an email or invoke a Lambda.

**11. Lambda not triggering from S3?**
I’d check S3 event notification settings, Lambda’s permission to be invoked, and CloudWatch logs for errors.

**12. CloudWatch metrics and alarms use?**
Metrics track performance data (CPU, disk I/O), and alarms react to thresholds — e.g., send SNS alerts when utilization spikes.

**13. Real-time EC2 monitoring?**
Use CloudWatch metrics + dashboard, and optionally install the CloudWatch Agent for memory and disk metrics.

**14. Automatically delete old S3 files?**
Configure a lifecycle policy to delete or move files to Glacier after X days.

**15. Using tags effectively?**
Tag by project, environment, and owner for cost allocation and easy resource grouping in AWS Cost Explorer.

**16. CloudWatch vs CloudTrail?**
CloudWatch = performance monitoring; CloudTrail = records user/API actions for auditing.

**17. AWS cost spike troubleshooting?**
Use AWS Cost Explorer and look at “Service” and “Region” filters — often caused by large EBS or data transfer.

**18. Automate daily EC2 backups?**
Create snapshots using AWS Backup or a Lambda scheduled via EventBridge (CloudWatch Events).

**19. IAM for Jenkins deploys?**
Create an IAM user/role with limited deploy permissions, store credentials in Jenkins using Credentials Manager.

**20. Example POC (Lambda/CloudWatch)?**
I automated log cleanup using CloudWatch Events to trigger a Lambda that deletes old S3 logs weekly.

---

## ⚙️ **2. Jenkins (CI/CD Tools)**

**1. Freestyle vs pipeline job?**
Freestyle is UI-based and simple; pipeline uses code (`Jenkinsfile`), offering version control and automation flexibility.

**2. GitHub commit didn’t trigger build — why?**
Check webhook configuration, Jenkins URL accessibility, and GitHub credentials. Also ensure SCM polling is set.

**3. Automatic deployment after build?**
Use pipeline stages — build, test, deploy. Post-success, trigger Ansible/Terraform scripts or AWS CLI commands.

**4. Pass parameters to pipeline?**
Use `parameters {}` block in a declarative pipeline; accessed via `${params.paramName}` inside stages.

**5. CI/CD stages in your POC?**
Code checkout → build (Maven/NPM) → test → Docker build → push to ECR → deploy via Ansible/EKS.

**6. Docker integration?**
Install Docker plugin, build image in pipeline, and push to registry. Jenkins can run inside Docker too.

**7. Build failed — debug how?**
Check console output, workspace files, and environment variables. I often replay failed builds after minor fixes.

**8. Schedule job?**
Use CRON syntax under “Build periodically” or in pipeline with `triggers { cron('H 2 * * *') }`.

**9. Jenkinsfile purpose?**
Defines pipeline as code — helps in version control, reproducibility, and team collaboration.

**10. Environment variables?**
Global, node, or stage-level variables defined in Jenkins or pipeline environment block.

**11. Plugins used?**
Git, Pipeline, Docker, Blue Ocean, AWS, Ansible, Credentials Binding — commonly used in DevOps setups.

**12. Secure credentials in Jenkins?**
Use Jenkins Credentials Manager; reference credentials with `withCredentials` block in pipeline.

**13. Rollback failed deployment?**
Implement version tracking; if deploy fails, redeploy last stable build automatically from artifact storage.

**14. Webhooks — how do they work?**
GitHub sends a POST to Jenkins whenever code changes — Jenkins triggers builds immediately.

**15. Auto build/test after every commit?**
Use GitHub webhook + Jenkins pipeline + test stage. CI ensures quick feedback.

**16. High availability Jenkins?**
Use master-agent architecture, store Jenkins home on EFS/S3, and run agents on multiple nodes.

**17. Declarative vs scripted pipelines?**
Declarative = structured, readable; Scripted = flexible Groovy-based. I prefer declarative for standard CI/CD.

**18. Integrate Jenkins with Ansible/Terraform?**
Post-build step calls `ansible-playbook` or `terraform apply` commands using Jenkins agents.

**19. Track build artifacts?**
Archive using “Archive the artifacts” or push to S3/Nexus for traceability.

**20. POC usage?**
Built a Jenkins pipeline to deploy Dockerized app to AWS EKS automatically after each code push.

---

## 🧱 **3. Terraform (Infrastructure as Code)**

**1. What problem does Terraform solve?**
Eliminates manual provisioning by defining infrastructure as code — consistent, repeatable, and version-controlled.

**2. What is a provider?**
Plugin for a specific platform (AWS, Azure, etc.) that lets Terraform manage its resources.

**3. Files created after init?**
`.terraform/`, `terraform.lock.hcl`, and `provider` plugin binaries.

**4. Plan vs apply?**
`plan` = preview of changes, `apply` = executes those changes on real infrastructure.

**5. Reuse configurations?**
Use modules and variables to parameterize and share code across environments.

**6. Purpose of `terraform.tfstate`?**
Tracks real resource mappings to avoid duplicate creation and manage updates.

**7. Deleted state file — effect?**
Terraform loses track of resources, may recreate them — always store state remotely (e.g., S3 backend).

**8. Pass variables?**
Use `.tfvars` files or CLI flags `-var "name=value"`.

**9. Handle multiple environments?**
Use workspaces (`terraform workspace`) or separate folders for dev/test/prod.

**10. Outputs usage?**
Expose key values (IP, instance ID) post-deployment for reuse in CI/CD pipelines.

**11. Local vs remote backends?**
Local stores state on disk; remote (S3, Terraform Cloud) supports collaboration and locking.

**12. Destroy infrastructure?**
`terraform destroy` tears down all managed resources safely.

**13. Can Terraform manage manually created EC2?**
Yes, with `terraform import` to bring it under Terraform management.

**14. Manage secrets?**
Use environment variables, AWS Secrets Manager, or Vault — never hardcode keys.

**15. Version control Terraform?**
Store in Git with `.tf` and `.tfvars` files; ignore `.terraform/`.

**16. Handle dependencies?**
Terraform automatically infers from resource references (like `aws_instance depends_on`).

**17. Terraform POC example?**
Created an EC2 + S3 setup using Terraform modules and triggered via Jenkins pipeline.

**18. Jenkins + Terraform integration?**
Jenkins executes Terraform commands in pipeline to deploy infra automatically.

**19. Debug failed apply?**
Use `TF_LOG=DEBUG` and validate syntax before apply.

**20. Best practices?**
Use modules, remote state, version locking, and least-privilege IAM.

---

## 🧩 **4. Ansible (Configuration Management)**

**1. What is Ansible?**
Agentless automation tool used for configuration, app deployment, and orchestration using SSH.

**2. Playbooks & Inventories?**
Playbooks = YAML scripts defining tasks; Inventory = list of managed hosts.

**3. Connect to remote servers?**
Via SSH using keys, or with `ansible_user` and `ansible_host` defined in inventory.

**4. Modules?**
Reusable units like `yum`, `service`, `copy`, `template` — perform specific actions.

**5. Difference from Terraform?**
Terraform = provisioning infra; Ansible = configuring it post-provisioning.

**6. Reusable playbooks?**
Use roles, variables, and includes to modularize playbooks.

**7. Storing credentials?**
Use Ansible Vault to encrypt sensitive data like passwords or keys.

**8. Ansible Role?**
Structured way to organize playbooks into reusable components — tasks, vars, handlers.

**9. Deploy applications?**
Automate package installs, config files, and service restarts via playbooks.

**10. Check syntax errors?**
Run `ansible-playbook --syntax-check playbook.yml`.

**11. Task failure handling?**
Use `ignore_errors: yes` or handlers to continue or recover.

**12. Handlers & notify?**
Handlers run only when triggered — e.g., restart Nginx after config change.

**13. Ad-hoc commands?**
Quick, one-line automation: `ansible all -m ping`.

**14. Run specific task?**
Use tags and `--tags` flag to run particular tasks only.

**15. Example POC?**
Automated Nginx setup across EC2s using roles, vars, and handlers.

**16. Jenkins integration?**
Jenkins triggers playbook execution after a build — via `ansible-playbook` step.

**17. Prevent config drift?**
Re-run playbooks regularly — idempotency ensures consistent state.

**18. Ansible facts?**
System details (OS, memory, IP) gathered automatically before task execution.

**19. Organize large projects?**
Use roles, inventories, group_vars, and playbook structure for clarity.

**20. Debug failed runs?**
Use `-vvv` verbosity or `--step` to trace issues line by line.

---

