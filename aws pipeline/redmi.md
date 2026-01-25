
✅ IAM user create
✅ Policy attach
✅ CodeCommit repo create
✅ Local machine pe git setup
✅ Code push
✅ CodePipeline
✅ CodeBuild
✅ CodeDeploy
✅ EC2 deploy

# AWS CI/CD using CodeCommit (End to End Guide)

## Objective
Is guide ka goal hai:
- AWS CodeCommit se code push karna
- CI/CD pipeline banana
- EC2 instance pe automatically code deploy karna

---

## Architecture Flow

Developer Laptop
   ↓
AWS CodeCommit (Git Repo)
   ↓
AWS CodePipeline
   ↓
AWS CodeBuild
   ↓
AWS CodeDeploy
   ↓
EC2 Instance

---

## STEP 1: IAM User Create Karna

### 1.1 IAM User Banaye
1. AWS Console → IAM
2. Users → Create User
3. User name: `cicd-user`
4. Access type:
   - ✅ Programmatic access
   - ✅ AWS Management Console access
5. Password set karo

---

### 1.2 IAM Policies Attach Karna

Attach these policies:
- `AWSCodeCommitFullAccess`
- `AWSCodePipelineFullAccess`
- `AWSCodeBuildDeveloperAccess`
- `AWSCodeDeployFullAccess`
- `AmazonEC2FullAccess`
- `IAMReadOnlyAccess`

Create user complete karo.

---

## STEP 2: IAM User Login
1. IAM dashboard se **login URL** copy karo
2. Browser me open karo
3. `cicd-user` se login karo

---

## STEP 3: CodeCommit Repository Banana

1. AWS Console → CodeCommit
2. Create Repository
3. Repository name: `my-cicd-repo`
4. Create

---

## STEP 4: Git Credentials Setup

### 4.1 HTTPS Git Credentials
1. IAM → Users → cicd-user
2. Security Credentials
3. HTTPS Git credentials → Generate
4. Username & Password save kar lo

---

## STEP 5: Local Machine Git Setup

```bash
git --version
````

Agar git install nahi:

```bash
sudo yum install git -y
```

---

## STEP 6: CodeCommit Repo Clone Karna

```bash
git clone https://git-codecommit.<region>.amazonaws.com/v1/repos/my-cicd-repo
cd my-cicd-repo
```

Username & password wahi paste karo jo IAM se mila.

---

## STEP 7: Sample Application Create Karna

### index.html

```html
<h1>Hello from AWS CI/CD Pipeline</h1>
```

---

## STEP 8: appspec.yml File (Mandatory)

```yaml
version: 0.0
os: linux
files:
  - source: /
    destination: /var/www/html
hooks:
  AfterInstall:
    - location: scripts/restart.sh
      timeout: 300
```

---

## STEP 9: Deployment Script

`scripts/restart.sh`

```bash
#!/bin/bash
sudo systemctl restart httpd
```

```bash
chmod +x scripts/restart.sh
```

---

## STEP 10: Git Push Karna

```bash
git status
git add .
git commit -m "Initial commit"
git push origin main
```

---

## STEP 11: EC2 Instance Setup

1. Launch EC2 (Amazon Linux 2)
2. Security Group:

   * Port 22 (SSH)
   * Port 80 (HTTP)

---

## STEP 12: CodeDeploy Agent Install

```bash
sudo yum update -y
sudo yum install ruby wget -y
cd /home/ec2-user
wget https://aws-codedeploy-<region>.s3.<region>.amazonaws.com/latest/install
chmod +x install
sudo ./install auto
```

Check:

```bash
sudo service codedeploy-agent status
```

---

## STEP 13: EC2 IAM Role Attach Karna

Create Role:

* Service: EC2
* Policy: `AmazonEC2RoleforAWSCodeDeploy`

Attach role to EC2.

---

## STEP 14: CodeDeploy Application

1. AWS Console → CodeDeploy
2. Create Application
3. Platform: EC2/On-Prem
4. Deployment Group create karo
5. EC2 tag select karo

---

## STEP 15: CodeBuild Project

1. AWS Console → CodeBuild
2. Create Build Project
3. Source: CodeCommit
4. Environment: Amazon Linux 2
5. Buildspec:

```yaml
version: 0.2
phases:
  build:
    commands:
      - echo Build completed
```

---

## STEP 16: CodePipeline Create Karna

1. AWS Console → CodePipeline
2. Create Pipeline
3. Source Stage:

   * Provider: CodeCommit
4. Build Stage:

   * Provider: CodeBuild
5. Deploy Stage:

   * Provider: CodeDeploy

---

## STEP 17: Final Test 🎉

1. index.html change karo
2. Git push karo
3. Pipeline auto run hoga
4. EC2 public IP browser me open karo
5. New code live hoga 🎯

---

## DONE ✅

AWS CI/CD using CodeCommit successfully configured.

