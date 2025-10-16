
### 🔹 What is EBS (Elastic Block Store)?

* Persistent block storage for EC2 instances
* Survives instance stop/start and termination (unless deleted)
* Supports snapshot, backup, resizing, and attachment to multiple instances (one at a time)

### 🔹 What is Instance Store?

* Temporary block storage physically attached to the host
* Very fast, but data is lost on stop/terminate/reboot
* Cannot be detached or moved
* Used for temporary data like cache, scratch space, or buffers

---

## 🔄 Detach EBS Volume from One EC2 Instance and Attach to Another

### ✅ Step 1: Unmount Volume on Current Instance

```bash
sudo umount /your/mount/point
```

### ✅ Step 2: Detach from Current Instance

* Go to **EC2 Console > Volumes**
* Select volume → **Actions > Detach Volume**

### ✅ Step 3: Attach to New EC2 Instance

* Select volume → **Actions > Attach Volume**
* Choose instance and device name (e.g., `/dev/sdf`)

### ✅ Step 4: Mount on New EC2 Instance

```bash
# Check block devices
lsblk

# Create mount point
sudo mkdir /data

# Mount the device
sudo mount /dev/xvdf /data
```

---

## 📏 Resize EBS Volume & File System

### ✅ Step 1: Modify EBS Volume Size

* EC2 Console > Volumes > Select volume
* **Actions > Modify Volume**
* Enter new size and click Modify

### ✅ Step 2: Resize File System

#### 🔹 For `ext4`:

```bash
sudo growpart /dev/xvdf 1      # If partitioned
sudo resize2fs /dev/xvdf1
```

#### 🔹 For `xfs`:

```bash
sudo xfs_growfs -d /your/mount/point
```

---

## 🚀 Create & Use Instance Store Volume

### ⚠️ Note: You **must choose an instance type that supports instance store**, such as:

* `c5d`, `m5d`, `i3`, `r5d`, etc.

### ✅ Step 1: Launch EC2 with Instance Store

* Choose supported instance type (e.g., `i3.large`)
* In **Add Storage**, you’ll see an Instance Store automatically added

### ✅ Step 2: Format and Mount

```bash
# Check device name
lsblk

# Format and mount
sudo mkfs -t ext4 /dev/nvme1n1
sudo mkdir /scratch
sudo mount /dev/nvme1n1 /scratch
```

> ⚠️ All data on instance store is lost when EC2 is stopped or terminated.

---

## 🧠 Summary Table

| Operation                 | Command / Console Steps          |
| ------------------------- | -------------------------------- |
| Unmount EBS               | `sudo umount /mountpoint`        |
| Detach EBS                | Console → Volumes → Detach       |
| Attach EBS to another EC2 | Console → Volumes → Attach       |
| Mount Volume              | `sudo mount /dev/xvdf /data`     |
| Resize EBS volume         | Console → Modify Volume          |
| Resize ext4 FS            | `sudo resize2fs /dev/xvdf1`      |
| Resize xfs FS             | `sudo xfs_growfs -d /mountpoint` |
| Use instance store        | Format and mount `/dev/nvme1n1`  |

---
