
# 🚀 AWS CLI Setup Guide (Ubuntu) – Multi-Account Configuration

This guide explains how to **install**, **configure**, and **use** the AWS Command Line Interface (CLI) on **Ubuntu Linux**.  
It also covers how to add **three AWS accounts** and set one as the **default account**.

---

## 🧩 1. Install AWS CLI on Ubuntu

```bash
sudo apt update
sudo apt install unzip -y
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
aws --version
````

Expected output:

```
aws-cli/2.x.x Python/3.x.x Linux/5.x.x botocore/2.x.x
```

---

## ⚙️ 2. Configure AWS CLI (Default Profile)

Run the configuration command and enter your credentials:

```bash
aws configure
```

```
AWS Access Key ID [None]: <ACCESS_KEY_ID>
AWS Secret Access Key [None]: <SECRET_ACCESS_KEY>
Default region name [None]: us-east-1
Default output format [None]: json
```

Verify setup:

```bash
aws sts get-caller-identity
```

---

## 🌐 3. Add Multiple AWS Accounts

You can configure **three separate accounts** using named profiles.

```bash
aws configure --profile account1
aws configure --profile account2
aws configure --profile account3
```

Or edit configuration files directly:

### `~/.aws/credentials`

```ini
[account1]
aws_access_key_id = <ACCOUNT1_ACCESS_KEY_ID>
aws_secret_access_key = <ACCOUNT1_SECRET_ACCESS_KEY>

[account2]
aws_access_key_id = <ACCOUNT2_ACCESS_KEY_ID>
aws_secret_access_key = <ACCOUNT2_SECRET_ACCESS_KEY>

[account3]
aws_access_key_id = <ACCOUNT3_ACCESS_KEY_ID>
aws_secret_access_key = <ACCOUNT3_SECRET_ACCESS_KEY>
```

### `~/.aws/config`

```ini
[default]
region = us-east-1
output = json

[profile account1]
region = us-east-1
output = json

[profile account2]
region = us-west-2
output = json

[profile account3]
region = ap-south-1
output = json
```

Test each account:

```bash
aws sts get-caller-identity --profile account1
aws sts get-caller-identity --profile account2
aws sts get-caller-identity --profile account3
```

---

## ☁️ 4. Launch an EC2 Instance (Example with Ubuntu 22.04)

```bash
# Create a key pair
aws ec2 create-key-pair --key-name MyUbuntuKey \
  --query "KeyMaterial" --output text > MyUbuntuKey.pem
chmod 400 MyUbuntuKey.pem

# Create a security group
aws ec2 create-security-group --group-name MyUbuntuSG \
  --description "Allow SSH and HTTP access"

# Allow SSH and HTTP traffic
aws ec2 authorize-security-group-ingress --group-name MyUbuntuSG \
  --protocol tcp --port 22 --cidr 0.0.0.0/0
aws ec2 authorize-security-group-ingress --group-name MyUbuntuSG \
  --protocol tcp --port 80 --cidr 0.0.0.0/0

# Launch an instance (Ubuntu 22.04)
aws ec2 run-instances \
  --image-id ami-08c40ec9ead489470 \
  --count 1 \
  --instance-type t2.micro \
  --key-name MyUbuntuKey \
  --security-groups MyUbuntuSG
```

Check instance details:

```bash
aws ec2 describe-instances --query "Reservations[*].Instances[*].[InstanceId,State.Name,PublicIpAddress]" --output table
```

SSH into instance:

```bash
ssh -i MyUbuntuKey.pem ubuntu@<PUBLIC-IP>
```

Terminate when done:

```bash
aws ec2 terminate-instances --instance-ids <INSTANCE-ID>
```

---

## 🧭 5. Set Account 2 as Default

### Option 1 — Temporarily (per terminal session)

```bash
export AWS_PROFILE=account2
```

To confirm:

```bash
echo $AWS_PROFILE
```

To remove:

```bash
unset AWS_PROFILE
```

### Option 2 — Permanently (for all sessions)

Add this line to your `~/.bashrc`:

```bash
echo "export AWS_PROFILE=account2" >> ~/.bashrc
source ~/.bashrc
```

### Option 3 — Make account2 the default in configuration

Edit your `~/.aws/credentials`:

```ini
[default]
aws_access_key_id = <ACCOUNT2_ACCESS_KEY_ID>
aws_secret_access_key = <ACCOUNT2_SECRET_ACCESS_KEY>
```

---

## ✅ Summary

| Task                      | Command                            |
| ------------------------- | ---------------------------------- |
| Install AWS CLI           | `curl ... && sudo ./aws/install`   |
| Configure default account | `aws configure`                    |
| Add multiple accounts     | `aws configure --profile accountX` |
| Use specific account      | `aws s3 ls --profile account2`     |
| Set account2 as default   | `export AWS_PROFILE=account2`      |
| Verify active account     | `aws sts get-caller-identity`      |
| Launch EC2 instance       | `aws ec2 run-instances ...`        |

---

### 🧹 Cleanup (Remove AWS CLI if needed)

```bash
sudo ./aws/install --bin-dir /usr/local/bin --install-dir /usr/local/aws-cli --update
sudo rm -rf /usr/local/aws-cli
sudo rm /usr/local/bin/aws
```

like me to include a **ready-to-run `setup.sh` script** that automates the entire configuration (installing CLI, adding accounts, and setting account2 as default)?
```
