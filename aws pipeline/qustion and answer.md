
## AWS CodeCommit – Questions & Answers

### 1. What is AWS CodeCommit?

**Answer:**
AWS CodeCommit is a fully managed **source control service** that hosts secure Git repositories. It allows teams to store, manage, and collaborate on code without managing their own Git servers.

---

### 2. What are the benefits of using AWS CodeCommit?

**Answer:**

* Fully managed and highly available
* Secure (IAM-based access, encryption at rest and in transit)
* Scales automatically
* Integrated with other AWS services (CodeBuild, CodePipeline, etc.)
* No size limits on repositories

---

### 3. How is CodeCommit different from GitHub?

**Answer:**

* CodeCommit is **AWS-managed** and integrates natively with AWS services
* Uses **IAM** for authentication instead of GitHub accounts
* No public repositories (private by default)
* GitHub has better social/collaboration features

---

### 4. How do you authenticate to AWS CodeCommit?

**Answer:**
You can authenticate using:

* HTTPS with **IAM credentials**
* SSH with **SSH keys**
* AWS CLI credential helper

---

### 5. Is CodeCommit highly available?

**Answer:**
Yes. CodeCommit is **regionally highly available** and data is automatically replicated across multiple AZs.

---

## CI/CD in AWS – Questions & Answers

### 6. What is CI/CD?

**Answer:**
CI/CD stands for **Continuous Integration and Continuous Delivery/Deployment**.
It automates building, testing, and deploying applications whenever code changes.

---

### 7. What AWS services are used for CI/CD?

**Answer:**

* **CodeCommit** – Source control
* **CodeBuild** – Build and test
* **CodeDeploy** – Deployment
* **CodePipeline** – Orchestration
* **CloudWatch** – Monitoring
* **IAM** – Access control

---

### 8. What is AWS CodePipeline?

**Answer:**
AWS CodePipeline is a **fully managed CI/CD service** that automates the release process by defining stages like source, build, test, and deploy.

---

### 9. What are the stages of a typical AWS CI/CD pipeline?

**Answer:**

1. Source – CodeCommit/GitHub
2. Build – CodeBuild
3. Test – Automated tests
4. Deploy – CodeDeploy / ECS / Lambda
5. Monitor – CloudWatch

---

### 10. What is AWS CodeBuild?

**Answer:**
AWS CodeBuild is a **fully managed build service** that compiles source code, runs tests, and produces build artifacts using a `buildspec.yml` file.

---

### 11. What is `buildspec.yml`?

**Answer:**
A YAML file that defines build commands and phases:

* install
* pre_build
* build
* post_build
* artifacts

---

### 12. What is AWS CodeDeploy?

**Answer:**
AWS CodeDeploy automates application deployments to:

* EC2
* ECS
* Lambda
* On-premises servers

---

### 13. What deployment strategies does CodeDeploy support?

**Answer:**

* Blue/Green deployment
* Rolling deployment
* Canary deployment (Lambda/ECS)
* All-at-once

---

### 14. How does CodePipeline integrate with CodeCommit?

**Answer:**
When code is pushed to a CodeCommit repository, it triggers CodePipeline automatically using **CloudWatch Events**.

---

### 15. How do you secure a CI/CD pipeline in AWS?

**Answer:**

* Use **IAM roles** with least privilege
* Enable **encryption** (KMS)
* Store secrets in **AWS Secrets Manager**
* Enable **CloudTrail** for auditing
* Restrict access using VPC endpoints

---

## Scenario-Based Questions

### 16. What happens if a build fails in CodePipeline?

**Answer:**
The pipeline stops execution, marks the stage as failed, and sends notifications (SNS/CloudWatch).

---

### 17. Can CodePipeline deploy to Lambda?

**Answer:**
Yes. CodePipeline can deploy Lambda functions using CodeDeploy or native Lambda actions.

---

### 18. How do you rollback a failed deployment?

**Answer:**

* CodeDeploy automatically rolls back if configured
* Blue/Green deployments allow traffic switch-back
* Previous artifacts can be redeployed

---

### 19. How do you store environment variables securely in CodeBuild?

**Answer:**

* AWS Systems Manager Parameter Store
* AWS Secrets Manager
* Encrypted environment variables

---

### 20. Explain a simple AWS CI/CD workflow.

**Answer:**

1. Developer pushes code to CodeCommit
2. CodePipeline triggers
3. CodeBuild runs build & tests
4. CodeDeploy deploys to EC2/Lambda
5. CloudWatch monitors deployment

---

