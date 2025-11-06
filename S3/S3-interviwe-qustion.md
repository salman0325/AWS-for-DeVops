### 🧠 **1. What is Amazon S3?**

**Answer:**
Amazon S3 (Simple Storage Service) is an object storage service used to store and retrieve data like files, images, backups, and logs from anywhere on the internet.

---

### 💾 **2. What is the difference between S3 and EBS?**

**Answer:**

* **S3** → Object storage (stores files as objects, scalable, accessible over HTTP).
* **EBS** → Block storage (used as hard drives for EC2 instances).

---

### 🔐 **3. How do you make an S3 bucket or object public?**

**Answer:**

* Disable “Block all public access”
* Set a **Bucket Policy** or **Object ACL** to allow `s3:GetObject` for `"Principal": "*"`.

---

### 🗂️ **4. What is S3 versioning and why use it?**

**Answer:**
Versioning keeps multiple versions of an object in one bucket — helps protect data from accidental deletion or overwriting.

---

### 🧱 **5. What is the difference between S3 Standard, IA, and Glacier?**

**Answer:**

* **Standard:** Frequent access, low latency.
* **IA (Infrequent Access):** For less-used data, cheaper storage.
* **Glacier:** For long-term archive, very cheap but slow retrieval.

---

### ⚙️ **6. How can you transfer data to S3 from CLI?**

**Answer:**
Using AWS CLI commands:

* Upload: `aws s3 cp file.txt s3://bucket-name/`
* Sync: `aws s3 sync ./ s3://bucket-name/`

---

### 🚨 **7. What is an S3 bucket policy and how is it different from IAM policy?**

**Answer:**

* **Bucket Policy:** Applies permissions directly to a bucket or objects.
* **IAM Policy:** Applies permissions to users, groups, or roles.

---

