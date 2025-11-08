# 🌐 **What is a VPC?**

**VPC (Virtual Private Cloud)** is your **own private network inside AWS**.
You can think of it like your home Wi-Fi network — but in the cloud.

You decide:

* The IP range
* How many subnets (small networks) you have
* Which subnets can go to the internet
* Who can connect to whom

---

## 💡 **Why Do We Use VPC?**

| Reason            | Simple Explanation                               |
| ----------------- | ------------------------------------------------ |
| **Security**      | You control who can enter or leave your network  |
| **Isolation**     | Your network is separate from others in AWS      |
| **Customization** | You design your own IP range and routing         |
| **Scalability**   | You can add more subnets or servers later easily |

---

## 🧩 **Basic VPC Network Diagram**

```
          +------------------------------------+
          |            AWS Cloud               |
          |             VPC (10.0.0.0/16)      |
          |                                    |
          |   +--------------------------+     |
          |   | Public Subnet (10.0.1.0/24)|   |
          |   |  Internet Gateway (IGW)   |    |
          |   |  EC2 #1: Public Instance  |    |
          |   +---------------------------+    |
          |                                    |
          |   +--------------------------+     |
          |   | Private Subnet (10.0.2.0/24)|  |
          |   |  EC2 #2: Private Instance  |   |
          |   +---------------------------+    |
          +------------------------------------+
```

---

## ⚙️ **Step-by-Step Setup in AWS**

### **Step 1: Create VPC**

1. Open **VPC Console → Your VPCs → Create VPC**
2. Name: `My-VPC`
3. IPv4 CIDR block: `10.0.0.0/16`
4. Leave all else as default
5. Click **Create VPC**

---

### **Step 2: Create Subnets**

**Public Subnet**

1. Go to **Subnets → Create Subnet**
2. Choose VPC: `My-VPC`
3. Name: `Public-Subnet`
4. CIDR: `10.0.1.0/24`
5. AZ: choose one (e.g. `us-east-1a`)
6. Click **Create Subnet**

**Private Subnet**

1. Again click **Create Subnet**
2. Name: `Private-Subnet`
3. CIDR: `10.0.2.0/24`
4. AZ: same or different, your choice
5. Click **Create Subnet**

---

### **Step 3: Create Internet Gateway (IGW)**

1. Go to **Internet Gateways → Create Internet Gateway**

   * Name: `My-IGW`
2. Click **Attach to VPC → My-VPC**

---

### **Step 4: Create Route Tables**

**Public Route Table**

1. Go to **Route Tables → Create Route Table**

   * Name: `Public-RT`
   * VPC: `My-VPC`
2. Click on `Public-RT` → **Routes → Edit routes → Add route**

   * Destination: `0.0.0.0/0`
   * Target: `My-IGW`
   * Save
3. Go to **Subnet associations → Edit subnet associations**

   * Choose: `Public-Subnet`
   * Save

**Private Route Table**

1. Create another route table:

   * Name: `Private-RT`
   * VPC: `My-VPC`
2. Associate it with `Private-Subnet`

---

### **Step 5: Create EC2 Instances**

#### **Public EC2 Instance**

1. Go to **EC2 → Launch Instance**
2. Name: `Public-EC2`
3. AMI: Amazon Linux 2
4. Instance type: `t2.micro`
5. Network: `My-VPC`
6. Subnet: `Public-Subnet`
7. Enable **Auto-assign Public IP**
8. Security Group:

   * Allow **SSH (port 22)** from your IP
9. Click **Launch**

#### **Private EC2 Instance**

1. Launch another instance:

   * Name: `Private-EC2`
   * Same AMI, type `t2.micro`
   * Network: `My-VPC`
   * Subnet: `Private-Subnet`
   * **Do NOT assign public IP**
   * Security Group: Allow **SSH from Public-EC2’s private IP**
2. Launch

---

### **Step 6: Connect to Public Instance**

Open your terminal or command prompt:

```bash
ssh -i your-key.pem ec2-user@<Public-EC2-Public-IP>
```

✅ You are now inside your **public EC2**.

---

### **Step 7: Connect to Private Instance (from Public EC2)**

Find your **Private EC2’s private IP** (example: 10.0.2.10).

Now from inside Public-EC2 terminal:

```bash
ssh ec2-user@10.0.2.10
```

✅ If you connect successfully, your setup is correct.

---

### **Step 8 (Optional): Test Network**

**Ping private EC2**

```bash
ping 10.0.2.10
```

**Test internet from public EC2**

```bash
curl https://google.com
```

If private EC2 needs internet, you can add a **NAT Gateway** later.

---

## 🎯 **Final Result**

| Component        | Name           | CIDR        | Internet Access | Instance    |
| ---------------- | -------------- | ----------- | --------------- | ----------- |
| VPC              | My-VPC         | 10.0.0.0/16 | Controlled      | —           |
| Public Subnet    | Public-Subnet  | 10.0.1.0/24 | Yes             | Public-EC2  |
| Private Subnet   | Private-Subnet | 10.0.2.0/24 | No              | Private-EC2 |
| Internet Gateway | My-IGW         | —           | Yes             | —           |

✅ **Public EC2 → Private EC2 communication works**
✅ **Public EC2 → Internet works**

---

