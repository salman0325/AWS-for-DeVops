
# AWS RDS Setup and Connection via EC2

This guide explains how to:
1. Create an **RDS (Relational Database Service)** instance on AWS.
2. Connect to the RDS instance through an **EC2** instance.
3. Run **INSERT** and **DELETE** queries on the database.

---

## 🧩 Prerequisites

- An AWS account
- Basic understanding of EC2 and RDS
- AWS CLI installed (optional)
- MySQL client installed on EC2 (for MySQL-based RDS)

---

## ⚙️ Step 1: Create an RDS Instance

1. **Login** to the AWS Management Console.
2. Go to **RDS → Databases → Create database**.
3. Choose a **database engine** (e.g., MySQL, PostgreSQL).
4. Under **Templates**, choose:
   - **Free tier** (for testing) or **Production** (for real use).
5. In **Settings**:
   - DB instance identifier: `mydbinstance`
   - Master username: `admin`
   - Master password: `YourPassword123`
6. Choose **DB instance size** (e.g., `db.t3.micro`).
7. In **Connectivity**:
   - Select your **VPC**.
   - Choose **Public access = No** (recommended for security).
   - Under **VPC security groups**, select an existing one or create a new one.
8. Under **Database authentication**, keep **Password authentication**.
9. Click **Create database**.
10. Wait for the **RDS instance status** to become **available**.

---

## 🌐 Step 2: Configure Security Groups

1. Go to **EC2 → Security Groups**.
2. Find the **security group** attached to your **EC2 instance** and note its **Group ID**.
3. Now, edit the **RDS security group**:
   - In **Inbound rules**, add a new rule:
     - Type: **MySQL/Aurora** (port 3306)
     - Source: **EC2 security group ID**
   - Save rules.

This allows EC2 to connect to RDS securely.

---

## 💻 Step 3: Connect to EC2 Instance

1. Go to **EC2 → Instances**.
2. Select your instance and click **Connect**.
3. Use the AWS **EC2 Connect** console or **SSH**:
   ```bash
   ssh -i your-key.pem ec2-user@<EC2-Public-IP>
````

---

## 🧱 Step 4: Install MySQL Client on EC2

For **Amazon Linux**:

```bash
sudo yum update -y
sudo yum install mysql -y
```

For **Ubuntu**:

```bash
sudo apt update -y
sudo apt install mysql-client -y
```

---

## 🔗 Step 5: Connect to RDS from EC2

1. In the RDS console, copy the **endpoint** of your RDS instance (something like `mydbinstance.abc123xyz.us-east-1.rds.amazonaws.com`).
2. Run the following command on EC2:

```bash
mysql -h <RDS-ENDPOINT> -u admin -p
```

3. Enter your RDS password when prompted.

If successful, you’ll be inside the MySQL shell.

---

## 🧮 Step 6: Create a Database and Table

```sql
CREATE DATABASE company;
USE company;

CREATE TABLE employees (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    role VARCHAR(100)
);
```

---

## 📝 Step 7: Insert Data

```sql
INSERT INTO employees (name, role) VALUES ('Alice', 'Developer');
INSERT INTO employees (name, role) VALUES ('Bob', 'Designer');

SELECT * FROM employees;
```

Output:

```
+----+--------+-----------+
| id | name   | role      |
+----+--------+-----------+
|  1 | Alice  | Developer |
|  2 | Bob    | Designer  |
+----+--------+-----------+
```

---

## ❌ Step 8: Delete Data

```sql
DELETE FROM employees WHERE name = 'Alice';
SELECT * FROM employees;
```

Output:

```
+----+------+-----------+
| id | name | role      |
+----+------+-----------+
|  2 | Bob  | Designer  |
+----+------+-----------+
```

---

## 🧹 Step 9: Clean Up Resources

To avoid charges:

* Stop or delete your **RDS instance**.
* Terminate the **EC2 instance**.
* Delete unused **security groups**.

---

## ✅ Summary

You’ve successfully:

1. Created an RDS instance.
2. Connected to it securely from EC2.
3. Performed basic SQL operations (CREATE, INSERT, DELETE).

---

## 🧠 Tips

* Always use **IAM roles** for secure credential management.
* Use **parameter groups** for performance tuning.
* Backup your RDS using **automated backups or snapshots**.

---

**Author:** salman khan

