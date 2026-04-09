# 🚀 DevOps Interview Preparation Guide

## 📌 1. DevOps Basics

### ❓ What is DevOps?

DevOps is a combination of Development and Operations that focuses on automation, collaboration, and faster delivery of software.

### 🎯 Goal:

* Fast delivery
* High quality
* Automation

---

## 📌 2. CI/CD

### ❓ What is CI (Continuous Integration)?

Developers frequently merge code into a shared repository and it is automatically tested.

### ❓ What is CD?

* Continuous Delivery → Code is ready for deployment
* Continuous Deployment → Code is automatically deployed

### 🔄 CI/CD Pipeline Flow:

1. Code push
2. Build
3. Test
4. Deploy

### 💡 Example:

Using Jenkins pipeline:

```bash
git pull
mvn clean install
docker build -t app .
docker run -d app
```

---

## 📌 3. Git (Version Control)

### ❓ What is Git?

Git is a distributed version control system used to track code changes.

### ❓ git pull vs fetch

* git pull → fetch + merge
* git fetch → only download changes

### ❓ Merge Conflict?

When two changes affect the same line → needs manual resolution.

### 💡 Example:

```bash
git checkout -b feature
git add .
git commit -m "feature added"
git push origin feature
```

---

## 📌 4. Docker

### ❓ What is Docker?

Docker is a container platform to package applications with dependencies.

### ❓ Container vs VM

* Container → lightweight
* VM → heavy

### ❓ Image vs Container

* Image → template
* Container → running instance

### 💡 Example Dockerfile:

```dockerfile
FROM node:18
WORKDIR /app
COPY . .
RUN npm install
CMD ["npm", "start"]
```

---

## 📌 5. Kubernetes

### ❓ What is Kubernetes?

It is a container orchestration tool.

### ❓ Pod?

Smallest unit in Kubernetes (runs container).

### ❓ Deployment vs Service

* Deployment → manages pods
* Service → exposes pods

### 💡 Example YAML:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: app
  template:
    metadata:
      labels:
        app: app
    spec:
      containers:
      - name: app
        image: nginx
```

---

## 📌 6. AWS (Cloud)

### ❓ What is AWS?

Cloud platform that provides servers and services.

### Key Services:

* EC2 → virtual server
* S3 → storage
* IAM → access control

---

## 📌 7. Infrastructure as Code (IaC)

### ❓ What is IaC?

Managing infrastructure using code.

### ❓ Terraform?

Tool to automate infrastructure creation.

### 💡 Example:

```hcl
resource "aws_instance" "example" {
  ami           = "ami-123"
  instance_type = "t2.micro"
}
```

---

## 📌 8. Monitoring & Logging

### ❓ Monitoring?

Tracking system performance.

### ❓ Logging?

Recording system events.

### 💡 Tools:

* Prometheus
* Grafana

---

## 📌 9. Linux Basics

### Important Commands:

```bash
top        # CPU usage
ps aux     # running processes
df -h      # disk usage
chmod 777 file.txt
```

---

## 📌 10. Networking

### ❓ IP Address?

Unique address of a system.

### ❓ DNS?

Converts domain to IP.

### ❓ HTTP vs HTTPS

* HTTP → not secure
* HTTPS → secure

---

## 📌 11. Scenario-Based Questions

### ❓ Deployment fails – what will you do?

* Check logs
* Rollback
* Fix issue

### ❓ Server down?

* Check CPU/memory
* Restart service
* Check logs

### ❓ Pipeline slow?

* Optimize steps
* Use caching

---

## 🔥 Bonus Topics

### Blue-Green Deployment

Two environments → switch traffic.

### Canary Deployment

Deploy to small users first.

### Zero Downtime Deployment

Users are not affected during deployment.

