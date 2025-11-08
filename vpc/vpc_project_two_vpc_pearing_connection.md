# 📘 Project Overview

In this project, you will:

1. Create **two VPCs** (`VPC-A` and `VPC-B`)
2. Create **public and private subnets** in each VPC
3. Configure **Internet Gateways (IGW)** for public subnets
4. Set up **NAT Gateways** for private subnets
5. Establish **VPC Peering** between both VPCs
6. Update **Route Tables**, **Security Groups**, and **Network ACLs**
7. Test **connectivity** between instances in both VPCs
8. Verify everything works with **no overlapping CIDRs**

---

## 🧱 Prerequisites

- AWS Account (Administrator access)
- Basic understanding of:
  - VPC, Subnets, Route Tables, Gateways
  - EC2 Instances
- Region: `us-east-1` (for simplicity)
- Non-overlapping CIDR ranges

---

## 🌐 Step 0: Plan and Verify CIDR Blocks

Use online CIDR tools to plan non-overlapping networks:

🔗 CIDR Helper Tools:
- [https://cidr.xyz/](https://cidr.xyz/)
- [https://www.ipaddressguide.com/cidr](https://www.ipaddressguide.com/cidr)
- [https://subnet-calculator.com/](https://subnet-calculator.com/)

| VPC | CIDR Block | Public Subnet | Private Subnet |
|------|-------------|---------------|----------------|
| **VPC-A** | 10.0.0.0/16 | 10.0.1.0/24 | 10.0.2.0/24 |
| **VPC-B** | 10.1.0.0/16 | 10.1.1.0/24 | 10.1.2.0/24 |

---

## ⚙️ Step 1: Create VPC-A

1. Open **AWS Console → VPC → Create VPC**
2. Choose:
   - **Name tag:** `VPC-A`
   - **IPv4 CIDR block:** `10.0.0.0/16`
3. Click **Create VPC**

### Create Subnets
1. **Public Subnet A1**
   - CIDR: `10.0.1.0/24`
   - AZ: `us-east-1a`
   - Name: `Public-A1`
   - Enable **Auto-assign public IPv4**
2. **Private Subnet A2**
   - CIDR: `10.0.2.0/24`
   - AZ: `us-east-1a`
   - Name: `Private-A2`

### Create Internet Gateway
1. Go to **Internet Gateways → Create IGW**
   - Name: `IGW-VPC-A`
2. Attach it to `VPC-A`

### Create NAT Gateway
1. Launch **NAT Gateway** in `Public-A1`
2. Allocate **Elastic IP**
3. Name: `NAT-A`

### Update Route Tables
- **Public Route Table (VPC-A):**
  - Add route → `0.0.0.0/0 → IGW-VPC-A`
- **Private Route Table (VPC-A):**
  - Add route → `0.0.0.0/0 → NAT-A`

---

## ⚙️ Step 2: Create VPC-B

Repeat similar steps for the second VPC.

1. **Create VPC**
   - Name: `VPC-B`
   - CIDR: `10.1.0.0/16`

### Create Subnets
1. **Public Subnet B1**
   - CIDR: `10.1.1.0/24`
   - Name: `Public-B1`
   - Enable auto-assign public IP
2. **Private Subnet B2**
   - CIDR: `10.1.2.0/24`
   - Name: `Private-B2`

### Create Internet Gateway
1. Create and attach **IGW-VPC-B**

### Create NAT Gateway
1. Create **NAT-B** in `Public-B1`
2. Allocate Elastic IP

### Update Route Tables
- **Public RT-B:** `0.0.0.0/0 → IGW-VPC-B`
- **Private RT-B:** `0.0.0.0/0 → NAT-B`

---

## 🤝 Step 3: Create VPC Peering Connection

1. Go to **VPC → Peering Connections → Create Peering Connection**
2. Name: `vpc-a-to-vpc-b`
3. Requester VPC: `VPC-A`
4. Accepter VPC: `VPC-B`
5. Click **Create Peering Connection**
6. Accept the request from `VPC-B` side

✅ Ensure status = `Active`

---

## 🗺️ Step 4: Update Route Tables for Peering

### In VPC-A
- Edit **Public RT-A** and **Private RT-A**  
  Add:
  - **Destination:** `10.1.0.0/16`
  - **Target:** `pcx-xxxxxx` (your peering ID)

### In VPC-B
- Edit **Public RT-B** and **Private RT-B**  
  Add:
  - **Destination:** `10.0.0.0/16`
  - **Target:** `pcx-xxxxxx`

---

## 🔐 Step 5: Update Security Groups

In **both VPCs**, modify EC2 Security Groups to allow traffic from the peer VPC’s CIDR.

Example for VPC-A:
- **Inbound:** Source = `10.1.0.0/16`
- **Outbound:** Destination = `10.1.0.0/16`

Example for VPC-B:
- **Inbound:** Source = `10.0.0.0/16`
- **Outbound:** Destination = `10.0.0.0/16`

---

## 🧱 Step 6: (Optional) Update Network ACLs

Allow both CIDRs in the NACLs for inbound/outbound rules.

| Direction | Type | Source/Destination | Allow |
|------------|------|--------------------|--------|
| Inbound | All traffic | 10.1.0.0/16 | ✅ |
| Outbound | All traffic | 10.0.0.0/16 | ✅ |

---

## 🧪 Step 7: Launch EC2 Instances

### In VPC-A
- Public Subnet: `Public-A1`
- Private Subnet: `Private-A2`

### In VPC-B
- Public Subnet: `Public-B1`
- Private Subnet: `Private-B2`

> Use Amazon Linux 2 instances for simplicity.

Attach **key pairs** and **Security Groups** properly.

---

## 🧰 Step 8: Test Connectivity

### From EC2 in VPC-A (Public)
```bash
ping 10.1.1.10
````

### From EC2 in VPC-B (Private)

```bash
ping 10.0.2.10
```

### Common Checks if Failed

✅ Peering connection = Active
✅ CIDR ranges not overlapping
✅ Routes configured correctly
✅ Security Groups allow ICMP or TCP
✅ NACLs not blocking traffic

---

## ✅ Step 9: Verification Checklist

| Check                | Description                   | Status |
| -------------------- | ----------------------------- | ------ |
| 🔹 VPCs Created      | Two VPCs successfully created | ✅      |
| 🔹 Subnets           | Public & Private in each VPC  | ✅      |
| 🔹 IGW & NAT         | Configured properly           | ✅      |
| 🔹 Peering           | Active                        | ✅      |
| 🔹 Routing           | Correct                       | ✅      |
| 🔹 SGs & NACLs       | Permit communication          | ✅      |
| 🔹 Connectivity Test | Successful ping/curl          | ✅      |

---

## 📊 Step 10: Optional — Enable DNS Resolution Across VPCs

1. Go to **Peering Connection → Actions → Edit DNS Settings**
2. Enable:

   * **Allow DNS resolution from peer VPC**
3. Save changes

---

## 🧩 Network Topology Diagram (Text View)

```
                   +---------------------------+
                   |        VPC-A (10.0.0.0/16) |
                   |                           |
                   | Public-A1 (10.0.1.0/24)   |
                   | Private-A2 (10.0.2.0/24)  |
                   +---------------------------+
                             ||
                             ||  (VPC Peering)
                             ||
                   +---------------------------+
                   |        VPC-B (10.1.0.0/16) |
                   |                           |
                   | Public-B1 (10.1.1.0/24)   |
                   | Private-B2 (10.1.2.0/24)  |
                   +---------------------------+
```

---

## 🧠 Notes

* VPC Peering is **non-transitive** — each VPC needs its own connection.
* CIDR ranges **must not overlap**.
* For 3+ VPCs, use **AWS Transit Gateway** instead of peering.

---

## 🧰 Bonus: AWS CLI Commands (Optional)

```bash
# Create Peering
aws ec2 create-vpc-peering-connection \
  --vpc-id vpc-aaaa1111 \
  --peer-vpc-id vpc-bbbb2222 \
  --region us-east-1

# Accept Peering
aws ec2 accept-vpc-peering-connection \
  --vpc-peering-connection-id pcx-12345678
```

---

## 👨‍💻 Author

**Salman Khan**
Cloud & DevOps Engineer
📧 Contact: [cloudchamp0325@gmail.com]
---

✅ **Congratulations!**
You’ve successfully built a **dual VPC network with public and private subnets** and connected them using **VPC Peering** in AWS — with full routing, security, and connectivity.

```

---

Would you like me to add a **diagram image (AWS icons, VPCs, subnets, NAT, IGW, and peering)** to go with this README? It makes the project look professional for a GitHub portfolio.
```
