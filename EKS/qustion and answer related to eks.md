
## 🧩 **A. Scenario-Based EKS Questions (5)**

### 1️⃣ Scenario:

Your Pods are not connecting to the internet in an EKS cluster.
**Question:** What could be the issue?
**Answer:** The worker nodes may not have a **public subnet or NAT Gateway** configured properly for internet access.

---

### 2️⃣ Scenario:

You deployed a new NGINX pod, but it’s not reachable from outside.
**Question:** What should you do?
**Answer:** Expose it using a **Service of type LoadBalancer** or **Ingress** to make it publicly accessible.

---

### 3️⃣ Scenario:

EKS cluster creation failed with IAM permission errors.
**Question:** How do you fix it?
**Answer:** Attach proper **IAM policies** to the user or role running `eksctl`, such as `AmazonEKSClusterPolicy` and `AmazonEKSServicePolicy`.

---

### 4️⃣ Scenario:

You need to update your EKS cluster version.
**Question:** What command is used?
**Answer:**

```bash
eksctl upgrade cluster --name <cluster-name> --region <region>
```

---

### 5️⃣ Scenario:

One of your nodes shows `NotReady` in EKS.
**Question:** What could be the reason?
**Answer:**

* Node lost connection to cluster API
* `kubelet` service stopped
* IAM role misconfigured
* EC2 instance health issue

---

## ⚙️ **B. General EKS Questions (5)**

### 6️⃣ Question:

What is Amazon EKS?
**Answer:** A managed **Kubernetes service** by AWS that automatically handles cluster setup, scaling, and control plane management.

---

### 7️⃣ Question:

What is `eksctl` used for?
**Answer:** A simple CLI tool to **create, manage, and delete** EKS clusters easily.

---

### 8️⃣ Question:

What is the difference between EKS and ECS?
**Answer:**

* **EKS** = Kubernetes-based container orchestration
* **ECS** = AWS’s own container orchestration (non-Kubernetes)

---

### 9️⃣ Question:

Which command is used to see all pods in EKS?
**Answer:**

```bash
kubectl get pods -A
```

---

### 🔟 Question:

What is the purpose of a Node Group in EKS?
**Answer:** It defines a set of **EC2 instances (worker nodes)** that run the Kubernetes workloads (pods).

---

