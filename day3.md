Below is a ready-to-paste **README.md** content that collects all previous answers (AWS instance types summary, pricing options, how to create/access Ubuntu & Windows EC2, install Remmina & connect, instance metadata & user-data). Copy the whole block into `README.md`.

---

# AWS Quick Notes

## 1 — Common AWS EC2 Instance Types (short)

* **t3 / t4g** — General purpose — small websites, dev
* **m5 / m6** — Balanced — most apps, web servers
* **c5 / c6** — Compute optimized — CPU-heavy tasks
* **r5 / r6** — Memory optimized — large databases
* **x1 / x2** — Extra memory — in-memory DBs, big data
* **i3 / i4** — Storage optimized — fast I/O databases
* **g4 / g5** — GPU — ML, video, graphics
* **p3 / p4** — High-end GPU — deep learning
* **d3** — Dense storage — backups, big files
* **h1** — HDD storage — large datasets
* **z1** — High CPU + memory — simulations, CAD

---

## 2 — Pricing Options (short & to the point)

* **On-Demand** — Pay per hour/second. No commitment.
* **Reserved Instances (RI)** — 1 or 3 year commitment. Cheaper for steady workloads.
* **Savings Plans** — Commit $$/hour for 1 or 3 years. Flexible across EC2/Fargate/Lambda.

  * *Compute Savings Plan* — most flexible
  * *EC2 Instance Savings Plan* — cheaper, less flexible
* **Spot Instances** — Very cheap; AWS can reclaim anytime. Good for batch/non-critical jobs.
* **Dedicated Host** — Whole physical server for you. Use for license/compliance needs.

---

## 3 — Create & Access an **Ubuntu** EC2 Instance (step-by-step)

### Launch (console)

1. AWS Console → **EC2 → Launch Instances**
2. Name: `Ubuntu-Server`
3. AMI: **Ubuntu Server** (e.g., 22.04 LTS)
4. Instance type: `t2.micro` (free tier) or choice
5. Key pair: create/download `.pem`
6. Security group: allow **SSH (port 22)**
7. Launch

### Access (from Linux/macOS/Windows using WSL/Git Bash)

```bash
chmod 400 your-key.pem
ssh -i your-key.pem ubuntu@<your-public-ip>
```

---

## 4 — Create & Access a **Windows** EC2 Instance (step-by-step)

### Launch (console)

1. AWS Console → **EC2 → Launch Instances**
2. Name: `Windows-Server`
3. AMI: **Microsoft Windows Server** (e.g., 2022)
4. Instance type: `t2.micro` (or choice)
5. Key pair: select/create `.pem`
6. Security group: allow **RDP (port 3389)**
7. Launch

### Get password & connect

1. Wait ~3–5 minutes for initialization.
2. Select instance → **Connect** → **RDP Client**
3. Click **Get Password** → upload `.pem` → decrypt
4. Use username (usually `Administrator`) and the decrypted password.
5. Download RDP file or use an RDP client (Windows Remote Desktop or Remmina).

---

## 5 — Install Remmina on Linux & Connect to Windows EC2

### Install Remmina (Ubuntu/Debian)

```bash
sudo apt update
sudo apt install remmina -y
```

### Prepare Windows EC2

* Make sure **RDP (3389)** is allowed.
* Get instance **public IP**, **username** and **password** (from EC2 console → Get Password).

### Connect using Remmina (GUI)

1. Open `remmina`
2. New connection:

   * Protocol: **RDP**
   * Server: `<public-ip>`
   * Username: `Administrator`
   * Password: `<your-decrypted-password>`
3. Click **Connect**

### Connect using `xfreerdp` (CLI alternative)

```bash
sudo apt install freerdp2-x11 -y
xfreerdp /u:Administrator /p:'YourPassword' /v:<public-ip>
```

---

## 6 — Instance Metadata & User Data (Ubuntu)

### Instance Metadata (inside instance)

* Metadata base URL:

```
http://169.254.169.254/latest/meta-data/
```

* Examples:

```bash
curl http://169.254.169.254/latest/meta-data/
curl http://169.254.169.254/latest/meta-data/instance-id
curl http://169.254.169.254/latest/meta-data/public-ipv4
```

### User Data (run once at launch)

* Add script when launching instance (Advanced > User Data) — example for Ubuntu:

```bash
#!/bin/bash
apt update -y
apt install apache2 -y
echo "<h1>Hello from User Data</h1>" > /var/www/html/index.html
```

* Check user-data run output:

```bash
sudo cat /var/log/cloud-init-output.log
```

---

## 7 — Quick Tips

* Save your `.pem` securely. It's required to SSH and to get Windows password.
* Open only required ports in Security Group (SSH 22, RDP 3389, HTTP 80).
* Use Spot for non-critical batch jobs; use RI/Savings Plans for steady workloads.
* Use metadata for scripts that need instance info (instance-id, region, etc.).

---

If you want, I can:

* shorten this file more, or
* create a version in Urdu/Hinglish, or
* add Terraform/CLI examples for launching instances. Which would you like?

