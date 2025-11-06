# 🖥️ Host a Static Website on Amazon S3 + AWS CLI Guide

This guide will walk you through setting up a **static website** on **Amazon S3**, creating an **IAM user**, and using the **AWS CLI** to interact with your S3 bucket.

---

## 📋 Prerequisites

* An [AWS account](https://aws.amazon.com/)
* AWS CLI installed → [Install Guide](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
* Your website files (e.g., `index.html`, `style.css`, etc.)

---

## ⚙️ Step 1: Create an S3 Bucket

1. Go to the **AWS Management Console** → open **S3**.
2. Click **Create bucket**.
3. Enter a **unique bucket name** (e.g., `my-static-website-demo`).
4. Choose your preferred **AWS Region**.
5. **Uncheck** “Block all public access”.
6. Confirm you want to make the bucket public.
7. Click **Create bucket**.

---

## 🌐 Step 2: Enable Static Website Hosting

1. Open your new bucket → **Properties** tab.
2. Scroll to **Static website hosting**.
3. Click **Edit**, then:

   * Enable **Static website hosting**
   * Choose **Host a static website**
   * Specify:

     * **Index document**: `index.html`
     * **Error document**: `error.html` (optional)
4. Click **Save changes**.

At the bottom, note the **Bucket website endpoint** — this is your website URL.

---

## 🔒 Step 3: Set Up IAM User and Permissions

1. Go to **IAM Console** → **Users** → **Add users**.
2. Enter a username (e.g., `s3-cli-user`).
3. Select **Access key - Programmatic access**.
4. Click **Next** → **Attach existing policies directly**.
5. Choose **AmazonS3FullAccess** (for demo — use least privilege in production).
6. Click **Create user**.
7. Download the **Access key ID** and **Secret access key**.

---

## 🧰 Step 4: Configure AWS CLI

Open your terminal or PowerShell and run:

```bash
aws configure
```

Enter the credentials from the IAM user:

```
AWS Access Key ID [None]: YOUR_ACCESS_KEY
AWS Secret Access Key [None]: YOUR_SECRET_KEY
Default region name [None]: us-east-1
Default output format [None]: json
```

---

## 📤 Step 5: Upload Files to S3 via CLI

1. Navigate to your project folder:

   ```bash
   cd path/to/your/website
   ```

2. Upload files to your S3 bucket:

   ```bash
   aws s3 sync . s3://my-static-website-demo --acl public-read
   ```

   This command uploads all files from the current directory and makes them publicly readable.

---

## 📥 Step 6: Download Files from S3 via CLI

To download a specific file:

```bash
aws s3 cp s3://my-static-website-demo/index.html ./index.html
```

To download an entire bucket:

```bash
aws s3 sync s3://my-static-website-demo ./local-folder
```

---

## 🌍 Step 7: Access Your Website

Visit the **Bucket Website Endpoint** URL from Step 2.
Example:

```
http://my-static-website-demo.s3-website-us-east-1.amazonaws.com
```

Your static website should now be live!

---

## 🧹 Optional: Make Files Public via Bucket Policy

If your files aren’t loading, set a bucket policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-static-website-demo/*"
    }
  ]
}
```

Save this in **Permissions → Bucket Policy**.

---

## ✅ Summary of Key Commands

| Action               | Command                                           |
| -------------------- | ------------------------------------------------- |
| Configure AWS CLI    | `aws configure`                                   |
| Upload file          | `aws s3 cp file.txt s3://my-bucket/`              |
| Upload folder        | `aws s3 sync . s3://my-bucket/ --acl public-read` |
| Download file        | `aws s3 cp s3://my-bucket/file.txt ./`            |
| Download all         | `aws s3 sync s3://my-bucket ./local-folder`       |
| List buckets         | `aws s3 ls`                                       |
| List files in bucket | `aws s3 ls s3://my-bucket/`                       |

---

## 🚀 Done!

You’ve now:

* Created and configured an **S3 bucket**
* Set up **IAM access**
* Used **AWS CLI** to upload and download files
* Hosted a **static website** on **S3**

