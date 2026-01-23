
# 🛡️ AWS IAM (Identity and Access Management)

## 🔹 What is IAM?

**IAM (Identity and Access Management)** is an AWS service that helps you **manage access** to AWS resources securely.
It controls:

* **Who** can access (Users, Groups, Roles)
* **What** they can do (Policies)
* **How** they authenticate (Password, MFA)

---

## 👤 Create a User

1. Go to **AWS Console → IAM → Users → Create user**
2. Enter a **user name**
3. Choose **Access type**:

   * **Console access** → for AWS web console login
   * **Programmatic access** → for API/SDK use
4. Choose **Attach policies directly**
5. Select a policy like:

   * `AdministratorAccess` (full access — not recommended)
   * `ReadOnlyAccess` (for viewing only)
   * Or a **custom policy** for limited permissions
6. Review and **Create user**
7. Download credentials (if generated)

---

## 👥 Create a Group and Add Users

**Why Groups?**

* Easier permission management.
* Assign one policy to many users at once.

**Steps:**

1. Go to **IAM → User groups → Create group**
2. Enter a **group name**
3. Attach policies (e.g. `ReadOnlyAccess`, `S3FullAccess`)
4. Add users to the group
5. Create the group

Now all users in that group automatically get the same permissions.

---

## 📜 Attach a Policy to a User or Group

1. Go to **IAM → Users** (or **User groups**)
2. Select the user/group
3. Open the **Permissions** tab
4. Click **Add permissions → Attach policies directly**
5. Select a managed policy or create your own JSON policy
6. Save changes

---

## 🔑 Password Policy

**Purpose:** to enforce strong passwords across all IAM users.

**How to set it:**

1. Go to **IAM → Account settings → Password policy**
2. Enable options like:

   * Minimum password length (e.g. 12)
   * Require uppercase, lowercase, numbers, and symbols
   * Require password reset after first login
   * Prevent password reuse
3. Save the policy

**Why:** Improves security and prevents weak or reused passwords.

---

## 🔐 Multi-Factor Authentication (MFA)

**What is MFA?**

* Adds an extra layer of security (password + one-time code).
* Protects against stolen passwords.

**Steps to enable MFA:**

1. Go to **IAM → Users → [Select User] → Security credentials**
2. Click **Assign MFA device**
3. Choose **Virtual MFA device** (for example, Google Authenticator or Authy app)
4. Scan the QR code with the app
5. Enter two consecutive codes shown in the app
6. Click **Activate**

**Why needed:**

* Prevents unauthorized access even if passwords are compromised.
* Strongly recommended for **admin** and **root** users.

---

## 🧠 Best Practices Summary

* Always use **Groups** instead of assigning permissions directly to users.
* Follow **Least Privilege Principle** – give only required permissions.
* Enable **MFA** for all privileged accounts.
* Apply a **strong password policy**.
* Regularly review **user access** and **remove inactive users**.
* Never share root credentials.

---

