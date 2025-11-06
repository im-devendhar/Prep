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
---

## 🐳 Docker (Containerization & Orchestration)

1. **What is Docker and why is it used in DevOps?**
   Docker allows applications to run in isolated containers with their dependencies. It ensures consistency across environments — from development to production — and speeds up deployment.

2. **What’s the difference between an image and a container?**
   An image is a blueprint (read-only) for containers. A container is a running instance of that image. Multiple containers can be launched from the same image.

3. **How do you create a Dockerfile?**
   A Dockerfile contains instructions like `FROM`, `COPY`, `RUN`, and `CMD` to build images. Example: `FROM ubuntu:latest`, then install packages and copy your app.

4. **What’s the purpose of Docker Compose?**
   Docker Compose defines and runs multi-container applications using a `docker-compose.yml` file. It’s great for running services like web + DB locally.

5. **Explain a real use case where you used Docker in a project.**
   I containerized a web app to eliminate “works on my machine” issues. Using Docker, all developers ran the same environment — deployment became push-and-run.

6. **What is Docker Hub?**
   It’s a public registry for storing and sharing container images, similar to GitHub for code.

7. **How do you persist data in Docker?**
   By using volumes (`docker volume create`, `-v /path:/container/path`), ensuring data isn’t lost when containers restart.

8. **How do you connect multiple containers?**
   Using Docker networks — containers on the same network communicate by name instead of IP.

9. **How do you reduce the size of Docker images?**
   Use smaller base images like `alpine`, combine RUN commands, and remove temporary files after installations.

10. **What is the difference between CMD and ENTRYPOINT?**
    `CMD` provides default arguments; `ENTRYPOINT` defines the main executable. You can combine them for flexibility.

11. **What are the advantages of using Docker over virtual machines?**
    Docker containers are lightweight, start in seconds, and use fewer resources compared to VMs.

12. **How do you monitor Docker containers?**
    Use `docker stats`, `docker logs`, or integrate with Prometheus/Grafana for metrics.

13. **What happens when a container exits?**
    It moves to the “Exited” state; you can restart it with `docker start`.

14. **How do you clean up unused images and containers?**
    Use `docker system prune -a` carefully — it removes stopped containers, unused images, and networks.

15. **How do you handle secrets in Docker?**
    Avoid hardcoding credentials. Use environment variables or secret managers like AWS Secrets Manager or Docker Swarm secrets.

16. **What is a multi-stage build?**
    It allows you to build code in one stage and copy only the needed artifacts to a smaller final image — reducing size.

17. **What’s a health check in Docker?**
    Defined in the Dockerfile (`HEALTHCHECK CMD curl -f http://localhost || exit 1`) to monitor container health.

18. **How do you update a running container with new code?**
    Build a new image, stop the old container, and run the new one. You can automate this with Jenkins.

19. **Explain a Docker troubleshooting scenario.**
    Once, my container wouldn’t start due to port conflicts. I checked `docker ps` for ports and updated mappings — issue solved.

20. **How do you secure Docker containers?**
    Run as non-root, scan images for vulnerabilities, use trusted registries, and limit network access.

---

## 🏗️ Terraform (Infrastructure as Code)

1. **What is Terraform?**
   Terraform automates infrastructure provisioning using declarative `.tf` files. It supports multiple cloud providers.

2. **Explain the Terraform workflow.**
   Steps: `terraform init` → `plan` → `apply` → `destroy`. It’s like version-controlling infrastructure.

3. **What’s the purpose of a Terraform state file?**
   `terraform.tfstate` tracks deployed resources so Terraform knows what exists to manage future changes.

4. **What are providers in Terraform?**
   Plugins that let Terraform interact with cloud services like AWS, Azure, or GCP.

5. **What’s the difference between `terraform plan` and `apply`?**
   `plan` previews changes; `apply` executes them. It’s similar to a “dry run”.

6. **What is an output variable in Terraform?**
   It displays useful info after deployment, e.g., `output "public_ip" { value = aws_instance.web.public_ip }`.

7. **What are input variables?**
   They make templates reusable. Example: defining `variable "region" {}` and referencing it in the code.

8. **What is a module in Terraform?**
   A module is reusable Terraform code — like a function — to deploy consistent infrastructure pieces.

9. **What’s the use of `terraform import`?**
   To bring existing infrastructure under Terraform management without recreating it.

10. **How do you handle sensitive data in Terraform?**
    Use `sensitive = true` or store secrets in AWS Secrets Manager and reference them securely.

11. **How do you manage Terraform state in a team?**
    Store it remotely — e.g., in an S3 bucket with DynamoDB for state locking.

12. **What’s the use of `depends_on`?**
    It explicitly defines resource dependencies to control creation order.

13. **How do you destroy only specific resources?**
    Use `terraform destroy -target=aws_instance.example`.

14. **What is `terraform refresh`?**
    It updates the state file with the current infrastructure state.

15. **How do you handle versioning in Terraform?**
    Specify provider and Terraform versions in the configuration to avoid compatibility issues.

16. **Have you faced any Terraform errors?**
    Yes — once I got “state lock” errors. I released the lock from DynamoDB manually.

17. **What’s a backend in Terraform?**
    Defines where state is stored — e.g., local, S3, or Terraform Cloud.

18. **What are Terraform workspaces?**
    Used for environment segregation like dev, test, prod — all using the same codebase.

19. **How do you test Terraform configurations?**
    By using `terraform plan`, Terratest, or manually verifying resources post-deployment.

20. **What’s the advantage of Terraform over CloudFormation?**
    It’s multi-cloud, simpler syntax, and faster evolution in the community.

---

## ⚙️ Ansible (Configuration Management)

1. **What is Ansible?**
   Ansible automates configuration and application deployment using YAML playbooks — agentless and simple.

2. **How does Ansible connect to remote machines?**
   It uses SSH — no agent required — and executes commands from a control node.

3. **What are Ansible Playbooks?**
   YAML files that define automation steps — like installing packages or configuring servers.

4. **What are Ansible roles?**
   They organize playbooks into reusable components (tasks, templates, vars, handlers).

5. **How do you run a playbook?**
   Using the command: `ansible-playbook site.yml -i inventory`.

6. **What’s an inventory file?**
   It lists managed nodes — either static (`hosts` file) or dynamic (cloud-based).

7. **What is idempotency in Ansible?**
   It ensures that running the same playbook multiple times gives the same result — no duplication.

8. **How do you use variables in Ansible?**
   Define them in playbooks or inventory, and reference as `{{ variable_name }}`.

9. **What’s the difference between `copy` and `template` module?**
   `copy` transfers static files; `template` allows dynamic content using Jinja2.

10. **What’s a handler in Ansible?**
    Handlers run only when notified — e.g., restart a service after config changes.

11. **How do you check syntax before running a playbook?**
    Use `ansible-playbook --syntax-check site.yml`.

12. **How do you debug a playbook?**
    Add `-vvv` for verbose output or use the `debug` module.

13. **How do you secure secrets in Ansible?**
    With `ansible-vault` — it encrypts sensitive variables or files.

14. **What is a fact in Ansible?**
    System information collected automatically by Ansible from managed nodes.

15. **How do you limit a playbook to one host?**
    Use `--limit hostname` with the playbook command.

16. **What are tags in Ansible?**
    They allow running specific tasks — `--tags "install"` to run only install-related ones.

17. **How do you handle failed tasks?**
    With `ignore_errors: yes` or conditional execution using `when`.

18. **What is `register` used for?**
    To store the output of a task for use in later tasks.

19. **What’s the difference between `shell` and `command` module?**
    `shell` executes through a shell (supports pipes/redirection), `command` doesn’t.

20. **Explain an Ansible use case you implemented.**
    I automated Tomcat installation and deployment on EC2s using a playbook — reducing manual setup time by 80%.

---

---

## 🐚 Bash / Shell Scripting

1. **What is a shell script and why do we use it?**
   A shell script automates repetitive Linux tasks like backups or deployments. It’s written in Bash (`.sh`) and executed line by line by the shell.

2. **How do you make a script executable?**
   Use `chmod +x script.sh`, then run it as `./script.sh`.

3. **What’s the difference between `$*` and `$@` in shell scripting?**
   `$*` treats all arguments as a single string, while `$@` treats them individually — helpful when looping through arguments.

4. **How do you accept user input in a script?**
   With `read var_name`. Example: `read name; echo "Hello $name"`.

5. **What’s the use of `set -e`?**
   It makes the script exit immediately if a command fails — useful for CI pipelines.

6. **How do you debug a shell script?**
   Use `bash -x script.sh` to trace command execution step-by-step.

7. **How can you schedule a script to run automatically?**
   Use **cron jobs** — edit with `crontab -e` and specify schedule, e.g., `0 2 * * * /home/user/backup.sh`.

8. **Explain a use case where you automated something with shell scripting.**
   I wrote a script to upload Nginx logs from EC2 to S3 daily and rotate them, ensuring disk cleanup automatically.

9. **What is `grep` used for?**
   It searches for patterns in text files. Example: `grep "error" /var/log/syslog`.

10. **How do you check if a file exists in a script?**
    Use condition:

```bash
if [ -f /path/file ]; then echo "Exists"; fi
```

11. **What’s the difference between `>` and `>>`?**
    `>` overwrites a file, `>>` appends to it.

12. **How do you count lines in a file?**
    `wc -l filename` gives the line count.

13. **How do you find running processes in a script?**
    `ps aux | grep process_name` or `pgrep`.

14. **How do you pass parameters to a script?**
    Using `$1`, `$2`, etc. Example: `./deploy.sh app1 prod`.

15. **How can you send output to both screen and file?**
    Use `tee`: `ls -l | tee output.txt`.

16. **What is the use of `trap` command?**
    It catches signals — for example, to clean up temporary files when script is interrupted.

17. **How do you run a script in the background?**
    Add `&` at the end — `./script.sh &`.

18. **What’s the difference between `source script.sh` and `./script.sh`?**
    `source` runs in the current shell (affecting environment), while `./` runs in a new subshell.

19. **How do you extract specific columns from a file?**
    Use `awk '{print $2}' file.txt` or `cut -d',' -f1`.

20. **Explain a real-world Bash automation scenario.**
    I automated cleanup of unused AWS resources by running a daily Python + Bash hybrid script that used Boto3 APIs to detect idle EC2s.

---

## 🐧 Linux (Ubuntu / RedHat)

1. **How do you check system uptime?**
   Use `uptime` — it shows how long the system has been running.

2. **How do you check disk usage?**
   `df -h` shows filesystem usage; `du -sh folder/` shows folder size.

3. **How do you monitor running processes?**
   Use `top` or `htop` for live process monitoring.

4. **How do you find which process is using a specific port?**
   `sudo lsof -i :8080` or `netstat -tulnp | grep 8080`.

5. **How do you check memory usage?**
   `free -h` or `vmstat`.

6. **How do you manage services?**
   `systemctl start|stop|status nginx`.

7. **How do you find large files consuming space?**
   `find / -type f -size +100M` lists files over 100 MB.

8. **What is the use of `/etc/fstab`?**
   It defines filesystems to mount automatically on boot.

9. **How do you check CPU usage by process?**
   `top` or `ps -eo pid,comm,%cpu --sort=-%cpu`.

10. **What’s the difference between hard and soft links?**
    Hard link is an exact file copy; soft link (symlink) points to the original file.

11. **How do you change permissions?**
    `chmod 755 file` — 7 means read/write/execute for owner, 5 for others.

12. **How do you change ownership?**
    `chown user:group file`.

13. **What’s the purpose of `/etc/passwd`?**
    It stores user account info.

14. **How do you create and extract tar files?**
    Create: `tar -cvf backup.tar folder/`; Extract: `tar -xvf backup.tar`.

15. **How do you schedule jobs in Linux?**
    Using `cron` or `at`. Example: `crontab -e`.

16. **How do you check system logs?**
    `/var/log/syslog` or `/var/log/messages`.

17. **How do you check open ports?**
    `netstat -tulpn` or `ss -tuln`.

18. **How do you check which users are logged in?**
    `who` or `w`.

19. **What is SELinux?**
    A security module controlling access policies — can be enforcing, permissive, or disabled.

20. **Explain a Linux troubleshooting example.**
    My web app failed to start due to port conflict — used `netstat` to identify the process and changed the port in config.

---

## 📊 Monitoring & Logging (CloudWatch, ELK, New Relic)

1. **What is the importance of monitoring in DevOps?**
   It ensures system health, detects failures early, and supports proactive alerting.

2. **What is Amazon CloudWatch?**
   It’s AWS’s monitoring service — collects logs, metrics, and triggers alerts.

3. **How do you create CloudWatch alarms?**
   Set thresholds (e.g., CPU > 80%) and integrate with SNS for notifications.

4. **What are CloudWatch metrics?**
   Quantitative data like CPU, memory, or request count — viewable in dashboards.

5. **How do you monitor EC2 logs in CloudWatch?**
   Install the CloudWatch Agent to push `/var/log` files to log groups.

6. **What is an SNS topic?**
   A communication channel that sends alerts to subscribers (email, SMS, or Lambda).

7. **Explain event-driven automation using CloudWatch and SNS.**
   A CloudWatch alarm triggers SNS, which can call Lambda for automated remediation.

8. **What is the ELK stack?**
   Elasticsearch (store), Logstash (collect), Kibana (visualize) — for centralized log management.

9. **How do you ship logs to ELK?**
   Use Filebeat or Logstash to forward logs from servers.

10. **What’s a New Relic agent?**
    A lightweight agent installed on servers/apps that reports performance metrics.

11. **How do you create dashboards in New Relic?**
    Select metrics (CPU, memory, transactions) and add visual widgets for monitoring.

12. **What is alert fatigue and how do you prevent it?**
    Too many alerts cause desensitization. Fix by setting meaningful thresholds.

13. **How do you integrate Slack with monitoring tools?**
    Use webhooks or SNS subscriptions to send alerts directly to Slack channels.

14. **What is synthetic monitoring?**
    Simulates user actions to test application availability and response time.

15. **What’s the use of log aggregation?**
    Combining logs from multiple sources simplifies debugging and analysis.

16. **How do you troubleshoot high CPU alerts?**
    Check top processes with `top`, analyze logs, and scale instances if needed.

17. **How do you monitor Docker containers?**
    Use `docker stats`, Prometheus, or CloudWatch container insights.

18. **How do you measure application latency?**
    Track response time metrics using APM tools like New Relic or CloudWatch.

19. **Explain a scenario where monitoring saved downtime.**
    CloudWatch alerted high CPU — scaling policy added instances automatically, preventing failure.

20. **What’s the role of tagging in monitoring?**
    Tags help organize metrics/logs per environment, making dashboards clearer.

---

## 🧩 Git & GitHub (Version Control)

1. **What is Git?**
   A distributed version control system for tracking code changes.

2. **How do you clone a repository?**
   `git clone <repo_url>` creates a local copy of a remote repo.

3. **What’s the difference between Git and GitHub?**
   Git is the tool; GitHub hosts repositories online with collaboration features.

4. **How do you check branch list?**
   `git branch` shows local branches, `git branch -r` shows remote ones.

5. **How do you create and switch branches?**
   `git checkout -b feature1` creates and switches to the new branch.

6. **How do you merge branches?**
   Switch to target branch and run `git merge feature1`.

7. **What’s a pull request (PR)?**
   A request to merge code into another branch — allows code review before merging.

8. **How do you resolve merge conflicts?**
   Edit conflicting files, keep correct code, then `git add` and `git commit`.

9. **What is `git stash` used for?**
   Temporarily saves uncommitted changes — handy when switching branches.

10. **What’s the use of `.gitignore`?**
    To exclude unwanted files (like logs or configs) from version control.

11. **How do you undo the last commit?**
    `git reset --soft HEAD~1` keeps changes; `--hard` removes them.

12. **What’s the difference between `fetch` and `pull`?**
    `fetch` downloads updates; `pull` downloads and merges them.

13. **What’s the purpose of rebasing?**
    It cleans up commit history by integrating changes linearly.

14. **How do you tag releases?**
    `git tag -a v1.0 -m "Release v1.0"` marks a specific commit.

15. **What’s `git cherry-pick` used for?**
    To apply a specific commit from one branch to another.

16. **How do you check commit history?**
    `git log --oneline` shows concise commit history.

17. **What’s a detached HEAD state?**
    When you’re viewing a commit directly, not a branch — avoid committing in this state.

18. **How do you revert a pushed commit?**
    Use `git revert <commit_id>` — creates a new commit that undoes previous changes.

19. **What’s Git branching strategy?**
    Feature, develop, main branches — ensures isolated development and stable releases.

20. **Explain a Git scenario you handled.**
    I resolved a merge conflict during Jenkins pipeline deployment — rebased feature branch and retriggered CI successfully.

---



---

## 🧩 **1️⃣ Jenkinsfile (Declarative Pipeline)**

### ✅ **Example Jenkinsfile**

```groovy
pipeline {
    agent any

    environment {
        AWS_REGION = "us-east-1"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/devendhar/sample-app.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Docker Build & Push') {
            steps {
                sh '''
                docker build -t myapp:latest .
                docker tag myapp:latest 123456789.dkr.ecr.us-east-1.amazonaws.com/myapp:latest
                docker push 123456789.dkr.ecr.us-east-1.amazonaws.com/myapp:latest
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh 'ansible-playbook deploy.yml'
            }
        }
    }

    post {
        always {
            echo "Build and deployment finished!"
        }
    }
}
```

### 🧠 **Explanation**

* **`pipeline`**: Defines the CI/CD process.
* **`agent any`**: Runs the pipeline on any available Jenkins node.
* **`stages`**: Each step in the lifecycle (build, test, deploy).
* **`post`**: Always runs (for cleanup or notifications).
* Common question: *“How will you trigger Jenkins automatically after commit?”*
  → Use **GitHub webhook** to trigger Jenkins job on `git push`.

---

## 🐳 **2️⃣ Dockerfile**

### ✅ **Example Dockerfile**

```dockerfile
# Start with a base image
FROM python:3.9-slim

# Set working directory
WORKDIR /app

# Copy application files
COPY . /app

# Install dependencies
RUN pip install -r requirements.txt

# Expose application port
EXPOSE 5000

# Run the application
CMD ["python", "app.py"]
```

### 🧠 **Explanation**

* **`FROM`**: Base image — here we used Python 3.9.
* **`WORKDIR`**: Directory inside the container.
* **`COPY`**: Copies code from local machine to image.
* **`RUN`**: Executes commands at build time.
* **`CMD`**: Defines default container start command.

👉 Interview Tip: If they ask *“How will you reduce image size?”*
→ Use `alpine` base images and multi-stage builds.

---

## ☁️ **3️⃣ Terraform Script (IaC)**

### ✅ **Example – Create EC2 + S3 Bucket**

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "web" {
  ami           = "ami-0c02fb55956c7d316"
  instance_type = "t2.micro"
  tags = {
    Name = "WebServer"
  }
}

resource "aws_s3_bucket" "logs" {
  bucket = "my-logs-devendhar"
  acl    = "private"
}
```

### 🧠 **Explanation**

* **`provider`**: Specifies cloud provider (AWS).
* **`resource`**: Defines what to create — EC2 instance, S3 bucket, etc.
* **`terraform init`** → initializes.
* **`terraform plan`** → shows what will change.
* **`terraform apply`** → provisions resources.

💡 Tip: Keep `terraform.tfstate` secure and use remote backend (like S3).

---

## ⚙️ **4️⃣ Ansible Playbook**

### ✅ **Example Playbook – Install Apache**

```yaml
---
- name: Deploy Web Server
  hosts: webservers
  become: yes

  tasks:
    - name: Install Apache
      apt:
        name: apache2
        state: present
        update_cache: yes

    - name: Start Apache service
      service:
        name: apache2
        state: started
        enabled: yes
```

### 🧠 **Explanation**

* **`hosts`**: Defines target machines (from inventory).
* **`become: yes`**: Runs with sudo privileges.
* **`tasks`**: Each operation (install, configure, restart).
* **Modules**: `apt`, `yum`, `copy`, `service`, `template`, etc.
  💡 Common follow-up: *“How do you pass variables to playbooks?”*
  → Use `vars` or `--extra-vars` option.

---

## 🐚 **5️⃣ Bash Script**

### ✅ **Example – Upload Logs from EC2 to S3**

```bash
#!/bin/bash

BUCKET="logs-devapp"
LOG_DIR="/var/log/nginx"
DATE=$(date +%d%m%Y)

tar -czf /tmp/nginx_logs_$DATE.tar.gz $LOG_DIR

aws s3 cp /tmp/nginx_logs_$DATE.tar.gz s3://$BUCKET/nginx_logs_$DATE.tar.gz --region us-east-1

echo "Logs uploaded successfully for $DATE"
```

### 🧠 **Explanation**

* Compresses logs, uploads to S3, and prints confirmation.
* Scheduled via `cron` for automation.
  💡 Use `set -e` for fail-fast, and logging for monitoring.

---

## 🧱 **6️⃣ Kubernetes YAML Files**

### ✅ **Pod YAML**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
    - name: nginx
      image: nginx:latest
      ports:
        - containerPort: 80
```

### ✅ **Deployment YAML**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
        - name: web
          image: nginx:latest
          ports:
            - containerPort: 80
```

### ✅ **Service YAML**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-service
spec:
  selector:
    app: web
  ports:
    - port: 80
      targetPort: 80
  type: LoadBalancer
```

### 🧠 **Explanation**

* **Pod**: Smallest deployable unit (single container).
* **Deployment**: Manages Pods, scaling, rolling updates.
* **Service**: Exposes Pods (internally or externally).
* Use `kubectl apply -f file.yaml` to deploy.
  💡 Real scenario: *“How do you roll back to a previous deployment?”*
  → `kubectl rollout undo deployment/web-deployment`.

---

##  **Summary**

| Tool            | Purpose                     | Command to Run                 |
| --------------- | --------------------------- | ------------------------------ |
| **Jenkinsfile** | Defines CI/CD pipeline      | Configured in Jenkins job      |
| **Dockerfile**  | Builds container image      | `docker build -t app .`        |
| **Terraform**   | Provisions infrastructure   | `terraform init/plan/apply`    |
| **Ansible**     | Configures servers          | `ansible-playbook deploy.yml`  |
| **Bash**        | Automates tasks             | `bash script.sh`               |
| **Kubernetes**  | Deploys container workloads | `kubectl apply -f deploy.yaml` |

---


