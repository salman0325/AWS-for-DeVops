# Deploy a simple Node.js app to AWS ECR & ECS (with EC2, Docker, and CloudWatch)

> Complete step-by-step README to create an EC2 instance, install Docker & AWS CLI, configure AWS and IAM roles, build a small Node.js app, push a Docker image to ECR, create an ECS cluster & service, and verify in your browser. Includes CloudWatch logging setup.

---

## Overview — What are ECR, ECS and CloudWatch?

* **Amazon ECR (Elastic Container Registry)**: A fully-managed Docker container registry where you store, manage, and deploy Docker container images. Think of it as Docker Hub but managed by AWS and integrated with IAM and ECS.

* **Amazon ECS (Elastic Container Service)**: A container orchestration service that runs and manages containers on either EC2 instances (ECS on EC2) or serverless using AWS Fargate. ECS handles tasks, scheduling, clusters, and services.

* **Amazon CloudWatch**: Centralized logging, metrics, and monitoring for AWS resources. You can send container logs, host metrics, and ECS task metrics to CloudWatch to observe and debug your app.

---

## High-level flow

1. Launch an EC2 instance (Ubuntu) and install Docker + AWS CLI.
2. Prepare an IAM Role for the EC2 instance (or use your user credentials) with proper policies to allow ECR push and ECS actions.
3. Create a small Node.js application and Dockerfile locally (or on EC2).
4. Create an ECR repository and push the built image to ECR.
5. Create an ECS cluster and task definition (ECS on EC2 or Fargate). Register task and run a service.
6. Configure security groups and load balancer (optional) so the app is reachable in the browser.
7. Configure CloudWatch logs for ECS task containers.

---

## Prerequisites

* An AWS account with permission to create EC2, ECR, ECS, IAM roles, Security Groups, and CloudWatch.
* AWS CLI installed locally or on the EC2 instance (we include both console and CLI steps below).
* Basic knowledge of terminal/SSH.

---

## 1) Create an IAM role for EC2 (so EC2 can push to ECR)

### Console steps (recommended for beginners):

1. Open the AWS Management Console → IAM → Roles → Create role.
2. Choose **AWS service** → **EC2** → Next.
3. Attach these policies (at minimum):

   * `AmazonEC2ContainerRegistryFullAccess` (allows ECR push/pull)
   * `AmazonEC2FullAccess` or more restricted EC2 policies as needed
   * `CloudWatchLogsFullAccess` (so the instance can publish logs, optional but useful)
4. Give the role a name, e.g. `ec2-ecr-push-role` and create.
5. When launching EC2 later, attach this role to the instance.

> **Security note**: For production, scope down policies (least privilege). For a demo, `AmazonEC2ContainerRegistryFullAccess` is acceptable.

---

## 2) Launch an EC2 instance (Ubuntu) and install Docker + AWS CLI

### Launch EC2 (Console)

1. EC2 → Launch instance.
2. Choose **Ubuntu Server 22.04 LTS** (or 20.04) AMI.
3. Choose instance type `t3.micro` (free-tier eligible) or something suitable.
4. Under **Configure Instance**, attach the IAM role `ec2-ecr-push-role` you created.
5. Configure security group to allow inbound:

   * SSH (TCP 22) from your IP
   * HTTP (TCP 80) from 0.0.0.0/0 (to test the app in browser)
   * Or a custom port (e.g., 3000) if you want to expose app port directly
6. Launch and save the private key (.pem).

### Connect to EC2 (from your machine)

```bash
# set permissions and connect
chmod 400 mykey.pem
ssh -i mykey.pem ubuntu@<EC2_PUBLIC_IP>
```

### Install Docker & AWS CLI on EC2 (Ubuntu)

Run these commands on the EC2 instance (as `ubuntu` user with sudo):

```bash
# update
sudo apt update && sudo apt upgrade -y

# install docker
sudo apt install -y ca-certificates curl gnupg lsb-release
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
sudo usermod -aG docker $USER

# install AWS CLI v2
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

# verify
docker --version
aws --version
```

**Note:** If your EC2 instance has the IAM role attached, you do *not* need to run `aws configure` with static credentials. The instance will obtain temporary credentials automatically from the role.

---

## 3) Create a small Node.js application

Create a project directory on your local machine or on the EC2 instance.

**Files:** `package.json`, `app.js`, `Dockerfile`

`package.json`:

```json
{
  "name": "simple-node-ecs",
  "version": "1.0.0",
  "main": "app.js",
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "express": "^4.18.2"
  }
}
```

`app.js`:

```js
const express = require('express');
const app = express();
const port = process.env.PORT || 3000;

app.get('/', (req, res) => {
  res.send('Hello from Node.js app running in Docker on ECS!');
});

app.listen(port, () => {
  console.log(`Server listening on port ${port}`);
});
```

`Dockerfile`:

```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package.json package-lock.json* ./
RUN npm install --production
COPY . .
EXPOSE 3000
CMD ["node", "app.js"]
```

---

## 4) Create an ECR repository and push Docker image

There are two ways: **AWS Console** or **AWS CLI**. If you're on EC2 with the IAM role attached, the CLI method works without manual credentials.

### Using AWS CLI (on EC2 or locally after `aws configure`)

1. Create repository:

```bash
aws ecr create-repository --repository-name simple-node-ecs --region us-east-1
```

2. Authenticate Docker to ECR (CLI prints a login command). For example (replace region):

```bash
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <account-id>.dkr.ecr.us-east-1.amazonaws.com
```

3. Build and tag image:

```bash
docker build -t simple-node-ecs:latest .
# tag for ECR
docker tag simple-node-ecs:latest <account-id>.dkr.ecr.us-east-1.amazonaws.com/simple-node-ecs:latest
```

4. Push to ECR:

```bash
docker push <account-id>.dkr.ecr.us-east-1.amazonaws.com/simple-node-ecs:latest
```

**Replace `<account-id>` and `us-east-1` with your AWS account ID and region.**

### Using Console

* Go to ECR → Create repository → Name `simple-node-ecs`.
* ECR will show a set of commands (including docker login, build, tag, push). Follow those.

---

## 5) Create ECS Cluster and Task Definition

You can run ECS in two ways:

* **ECS on EC2** (you manage container instances/EC2 nodes)
* **ECS on Fargate** (serverless, AWS manages infrastructure)

We'll show a simple **Fargate** setup (simpler) and outline ECS-on-EC2 notes.

### Using the Console (Fargate)

1. Go to **ECS** → **Clusters** → **Create cluster** → Choose **Networking only (Fargate)** → Next.
2. Name the cluster (e.g., `simple-node-cluster`) and create.

### Create Task Definition (Fargate)

1. ECS → Task Definitions → Create new Task Definition → Fargate.
2. Task role: (Optional) choose a role that allows CloudWatch logs if you want. Execution role: `ecsTaskExecutionRole` (create if not present).
3. In Container definitions add a container:

   * Name: `simple-node-container`
   * Image: `<account-id>.dkr.ecr.us-east-1.amazonaws.com/simple-node-ecs:latest`
   * Port mappings: Container port `3000`.
   * Log configuration: **awslogs** — Log group `/ecs/simple-node-ecs` (or create new). Region: `us-east-1`, stream prefix `ecs`.
4. Save task definition.

### Create Service

1. Go to your cluster → Services → Create → Launch type: FARGATE.
2. Service name: `simple-node-service`.
3. Number of tasks: 1 (or more for redundancy).
4. Networking: choose a VPC and subnets and select a Security Group that allows inbound HTTP (80) if you use a load balancer or allow 3000 if exposing directly.
5. (Optional) Configure a load balancer (recommended): Create an Application Load Balancer (ALB) that listens on port 80 and forwards to target group on port 3000.
6. Create service — ECS will pull the image from ECR and start tasks.

---

## 6) ECS on EC2 (Alternative)

If you prefer to run ECS on EC2 (where you manage container instances):

* Create an ECS Optimized AMI EC2 instance (or install `ecs-agent` on regular EC2).
* Ensure instances are in same cluster via `ECS_CLUSTER` user-data or console.
* Register instance to ECS cluster and create task definition with launch type `EC2`.

For beginners, Fargate is recommended because it removes instance management.

---

## 7) Configure CloudWatch logs for your ECS containers

When creating the container definition in the task, under **Log configuration**, choose `awslogs` driver and set:

* Log group: `/ecs/simple-node-ecs` (create ahead in CloudWatch Logs or allow ECS to create it)
* Region: your region
* Stream prefix: `ecs`

You can then open CloudWatch → Logs → find the log group and view container logs (stdout/stderr).

---

## 8) Verify your app in the browser

* If you used an ALB: Get the ALB DNS name from EC2 → Load Balancers → Description → DNS name. Open `http://<ALB_DNS>` in your browser.
* If you used Fargate without ALB but assigned a public IP to the task: obtain the public IP from ECS task details and open `http://<PUBLIC_IP>:3000`.
* If using ECS on EC2 and exposed port 3000 via SG: open `http://<EC2_PUBLIC_IP>:3000`.

You should see: `Hello from Node.js app running in Docker on ECS!`

---

## 9) Useful CLI commands / Troubleshooting

* Check docker images: `docker images`
* View docker container logs (on EC2): `docker logs <container-id>`
* ECS describe tasks: `aws ecs describe-tasks --cluster simple-node-cluster --tasks <task-id>`
* CloudWatch logs: `aws logs tail /ecs/simple-node-ecs --follow`

### Common issues

* **ECR auth errors**: Re-run `aws ecr get-login-password ... | docker login ...`
* **Image pull failures**: Ensure the ECS execution role (`ecsTaskExecutionRole`) has `AmazonECSTaskExecutionRolePolicy` and ECR permissions.
* **Security group blocked**: Confirm inbound rules for ports (80 or 3000).
* **IAM permissions**: Make sure roles attached to EC2 or ECS tasks include the necessary policies.

---

## 10) Example: Minimal commands summary (quick)

```bash
# on EC2 (with IAM role attached):
# build and push image
docker build -t simple-node-ecs:latest .
docker tag simple-node-ecs:latest <account-id>.dkr.ecr.<region>.amazonaws.com/simple-node-ecs:latest
aws ecr get-login-password --region <region> | docker login --username AWS --password-stdin <account-id>.dkr.ecr.<region>.amazonaws.com
docker push <account-id>.dkr.ecr.<region>.amazonaws.com/simple-node-ecs:latest

# create Fargate task/service via console OR using AWS Copilot / CloudFormation / Terraform for automation
```

---

## Appendix A — Helpful permissions

* `AmazonEC2ContainerRegistryFullAccess` (or a scoped subset for push/pull)
* `AmazonECS_FullAccess` (or scoped ECS permissions)
* `CloudWatchLogsFullAccess` (or scoped to specific log groups)
* `AmazonEC2FullAccess` (for EC2 operations — scope down in production)

---

