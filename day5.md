
# AWS EC2 & EBS Cheat Sheet (Snapshots, Encryption, Backups, etc.)

This document summarizes key AWS EC2 and EBS concepts and tasks related to snapshot management, volume encryption, backup automation, and more.

---

## 📌 1. Resize AWS Root Volume

### Steps:
1. Stop the EC2 instance.
2. Modify the volume:
   - Go to EC2 → Volumes → Select Root Volume → **Modify Volume**
   - Set new size → Apply changes.
3. Start the EC2 instance.
4. Connect via SSH.
5. Expand the file system:
   ```bash
   sudo growpart /dev/xvda 1
   sudo resize2fs /dev/xvda1  # or xfs_growfs for XFS
````

---

## 🧾 2. Check File System on EBS Volume

```bash
df -Th
```

* Shows file system type, size, and usage.
* Common types: `ext4`, `xfs`.

---

## 🔗 3. Attach One EBS Volume to Multiple Instances?

* **Standard EBS volumes** can be attached to **only one instance at a time**.
* To attach to multiple instances, use:

  * **Amazon EFS** (for shared file systems)
  * **EBS Multi-Attach (io1/io2 only)**:

    * Only supported for some Linux file systems and applications like clustered databases.
    * File system must support concurrent writes (e.g., `OCFS2`, `GFS2`).

---

## 🔁 4. Change EC2 Instance Type

### Steps:

1. Stop the instance.
2. Go to EC2 Console → Instances → Select → Actions → **Instance Settings** → **Change Instance Type**.
3. Choose new type (e.g., t2.micro → t3.medium).
4. Start the instance.

---

## 💽 5. EBS Volume Types, Use Cases, and IOPS

| Volume Type             | Description               | Use Case                     | Max IOPS                   |
| ----------------------- | ------------------------- | ---------------------------- | -------------------------- |
| **gp3**                 | General Purpose SSD       | Default, cost-effective      | 16,000                     |
| **gp2**                 | Older general purpose SSD | Burstable, general workloads | 3,000                      |
| **io1/io2**             | Provisioned IOPS SSD      | High-performance DBs         | 64,000 (io2 Block Express) |
| **st1**                 | Throughput HDD            | Big data, log processing     | 500                        |
| **sc1**                 | Cold HDD                  | Infrequent access, archival  | 250                        |
| **magnetic (standard)** | Deprecated                | Legacy only                  | ~40–90                     |

---

## 📸 6. What is a Snapshot and How to Use It?

### A snapshot is a **point-in-time backup** of an EBS volume.

### Key Uses:

* Backup & recovery
* Create AMIs
* Cross-region volume copy
* Clone volumes for testing

### How to Create:

```bash
aws ec2 create-snapshot --volume-id vol-xxxx --description "My backup"
```

### Restore Snapshot:

```bash
aws ec2 create-volume --snapshot-id snap-xxxx --availability-zone us-east-1a
```

---

## 🕒 7. Automate EBS Volume Backup at 12 AM

Use **Amazon Data Lifecycle Manager (DLM)**:

### Steps:

1. Go to EC2 → Lifecycle Manager.
2. Create Policy:

   * Target volumes with tag (e.g., `Backup=Daily`)
   * Frequency: Every 24 hours
   * Time: 00:00 UTC (12 AM)
   * Retention: Keep N snapshots
3. Apply the tag to your volume.

---

## 🗑️ 8. Snapshot and AMI Recycle Bin

### Enables snapshot/AMI recovery after accidental deletion.

### How to Enable:

1. Go to EC2 → Recycle Bin → Create Retention Rule.
2. Choose resource type (`EBS Snapshot`, `AMI`).
3. Set retention period (e.g., 7 days).
4. Apply tags (optional).

### Restore:

* Go to **Recycle Bin Console** → Select snapshot → **Restore**

---

## 🌍 9. Copy Snapshot Across Regions

### Console:

* Snapshots → Select → **Actions → Copy Snapshot**
* Choose **destination region**, **KMS key**, etc.

### CLI:

```bash
aws ec2 copy-snapshot \
  --source-region us-east-1 \
  --source-snapshot-id snap-xxxx \
  --destination-region ap-south-1 \
  --description "Cross-region backup"
```

---

## 🔐 10. What Happens When You Encrypt an EBS Volume?

* Data at rest is encrypted (AES-256).
* Snapshots from the volume are encrypted.
* Encrypted volumes must stay encrypted.
* Transparent to EC2 OS.
* Requires KMS key (default or custom).

---

## 🛡️ 11. Enable Default EBS Encryption for All New Volumes

### Console:

1. Go to EC2 → Settings → EBS Encryption
2. Enable encryption by default
3. Choose KMS key (default or custom)

### CLI:

```bash
aws ec2 enable-ebs-encryption-by-default
aws ec2 modify-ebs-default-kms-key-id \
  --kms-key-id arn:aws:kms:region:account-id:key/key-id
```

---

## 🧹 12. Delete EBS Snapshot

### Console:

* Go to EC2 → Snapshots → Select → **Actions → Delete Snapshot**

### CLI:

```bash
aws ec2 delete-snapshot --snapshot-id snap-xxxx
```

> Be careful: If snapshot is used by an AMI, deleting it may break the AMI.

---

## ✅ Best Practices Summary

* 🔒 Always enable encryption by default.
* 🧼 Automate snapshots using DLM.
* 🔁 Use cross-region snapshot copy for DR.
* 🛡️ Use Recycle Bin to protect critical snapshots.
* 📎 Tag resources for automation and management.

---


