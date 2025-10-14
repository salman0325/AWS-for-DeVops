

## 🛡️ What is a Security Group?

A **Security Group** is like a **firewall** for your server (like an EC2 instance in AWS).

* It controls **who can send traffic in (inbound)** and **who can get traffic out (outbound)**.
* It's used to **protect your server** from unwanted access.
* You attach it to your EC2 instance (or another service) when launching or later.

---

## 🔄 Inbound vs Outbound Rules

### 🔽 Inbound Rules:

* These **control what traffic is allowed **into** your server.
* Example: Allow port 22 from your IP to SSH into your EC2.

### 🔼 Outbound Rules:

* These **control what traffic is allowed **from** your server going out.
* Example: Allow all traffic so your server can access the internet (like installing packages).

### ❗IMPORTANT:

* **Security Groups are "default allow" only.**
* This means:

  * If you **don’t add a rule**, **traffic is denied**.
  * You can **only allow** traffic — **you can’t explicitly deny** it.

---

## ✅ Example: Simple Security Group

Let’s say you want to:

* Allow SSH (port 22) from your IP
* Allow HTTP (port 80) from anywhere (for a web server)
* Allow HTTPS (port 443) from anywhere

### Inbound Rules:

| Type  | Protocol | Port Range | Source    |
| ----- | -------- | ---------- | --------- |
| SSH   | TCP      | 22         | Your IP   |
| HTTP  | TCP      | 80         | 0.0.0.0/0 |
| HTTPS | TCP      | 443        | 0.0.0.0/0 |

### Outbound Rules:

| Type | Protocol | Port Range | Destination |
| ---- | -------- | ---------- | ----------- |
| All  | All      | All        | 0.0.0.0/0   |

---

## 🧾 How to Define a Security Group in Code (for Git Repo)

If you’re managing infrastructure in code (like using **Terraform**, **CloudFormation**, etc.), here’s a **Terraform** example:

```hcl
resource "aws_security_group" "web_sg" {
  name        = "web-server-sg"
  description = "Allow SSH, HTTP, HTTPS"
  vpc_id      = "vpc-abc123"

  ingress {
    description = "SSH from my IP"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["YOUR_IP/32"]
  }

  ingress {
    description = "HTTP from anywhere"
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    description = "HTTPS from anywhere"
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    description = "Allow all outbound traffic"
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "WebSecurityGroup"
  }
}
```

## Summary:

| Term             | Meaning                                                          |
| ---------------- | ---------------------------------------------------------------- |
| Security Group   | Firewall for your server (EC2, etc.)                             |
| Inbound Rule     | Controls what traffic can come **into** your server              |
| Outbound Rule    | Controls what traffic can go **out** of your server              |
| Default Behavior | Deny all traffic unless **allowed** (you can't deny, only allow) |
| Use in Git Repo  | Save as code (e.g. Terraform), commit and push                   |

---
