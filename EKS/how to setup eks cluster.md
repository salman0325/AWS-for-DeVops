# 🚀 AWS EKS Setup with NGINX Deployment and Load Balancer

This guide walks through setting up an **Amazon EKS cluster** from scratch using **AWS CLI**, **eksctl**, and **kubectl**.
You will deploy an **NGINX Pod**, expose it using a **LoadBalancer Service**, and test it from AWS.

---

## 🧩 Prerequisites

Before starting, make sure you have:

* An AWS account
* One **EC2 instance (Amazon Linux 2 or Ubuntu)** for setup
* IAM role/user with **EKS, EC2, IAM, and CloudFormation** permissions

---

## ⚙️ Step 1: Launch EC2 and Connect

1. Go to **AWS Console → EC2 → Launch Instance**
2. Choose **Amazon Linux 2** or **Ubuntu**
3. Instance type: `t2.medium` (recommended for EKS setup)
4. Add **Key Pair** and **Security Group**

   * Allow `SSH (22)` and `HTTP (80)`
5. Launch and connect:

   ```bash
   ssh -i your-key.pem ec2-user@<your-ec2-public-ip>
   ```

---

## 🧭 Step 2: Install AWS CLI

```bash
sudo apt update -y
sudo apt install unzip curl -y
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
aws --version
```

---

## 🧰 Step 3: Install eksctl

```bash
curl -sLO "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz"
tar -xvzf eksctl_$(uname -s)_amd64.tar.gz
sudo mv eksctl /usr/local/bin
eksctl version
```

---

## 🧩 Step 4: Install kubectl

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin
kubectl version --client
```

---

## 🔐 Step 5: Configure AWS CLI

Run the following and enter your credentials:

```bash
aws configure
```

You’ll be asked for:

```
AWS Access Key ID: <your-key>
AWS Secret Access Key: <your-secret>
Default region name: us-east-1
Default output format: json
```

---

## 🧑‍💻 Step 6: Create IAM Role for EKS

1. Go to **AWS Console → IAM → Roles → Create Role**
2. Choose **EKS - Cluster**
3. Attach policies:

   * `AmazonEKSClusterPolicy`
4. Create another role for **EKS Node Group**:

   * Attach:

     * `AmazonEKSWorkerNodePolicy`
     * `AmazonEC2ContainerRegistryReadOnly`
     * `AmazonEKS_CNI_Policy`

Remember both role names (e.g., `EKS-ClusterRole`, `EKS-NodeRole`).

---

## ☸️ Step 7: Create EKS Cluster

```bash
eksctl create cluster \
--name my-eks-cluster \
--region us-east-1 \
--nodegroup-name my-nodes \
--node-type t3.medium \
--nodes 2 \
--managed
```

✅ This will take 10–15 minutes to create:

* VPC
* Subnets
* Security Groups
* Cluster
* Node Group

---

## 🧾 Step 8: Verify Cluster

```bash
kubectl get nodes
```

You should see 2 nodes in `Ready` state.

---

## 🧱 Step 9: Create Namespace and Deployment (NGINX)

Create a file named `nginx-deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
  namespace: default
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```

Apply it:

```bash
kubectl apply -f nginx-deployment.yaml
```

Check status:

```bash
kubectl get pods
```

---

## 🌐 Step 10: Expose NGINX via LoadBalancer

Create a file `nginx-service.yaml`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: LoadBalancer
```

Apply it:

```bash
kubectl apply -f nginx-service.yaml
```

---

## 🧩 Step 11: Check Service and Load Balancer

```bash
kubectl get svc
```

Output example:

```
NAME             TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)        AGE
nginx-service    LoadBalancer   10.100.200.45   a1b2c3d4.elb.amazonaws.com   80:31586/TCP   2m
```

Copy the **EXTERNAL-IP** or **DNS name** (`a1b2c3d4.elb.amazonaws.com`)
Open it in your browser — you’ll see the **NGINX Welcome Page** 🎉

---

## 🧹 Step 12: Cleanup (Optional)

```bash
eksctl delete cluster --name my-eks-cluster --region us-east-1
```

---

## ✅ Summary

| Step | Action               | Tool Used     |
| ---- | -------------------- | ------------- |
| 1    | Launch EC2           | AWS Console   |
| 2    | Install AWS CLI      | CLI           |
| 3    | Install eksctl       | CLI           |
| 4    | Install kubectl      | CLI           |
| 5    | Configure AWS        | aws configure |
| 6    | Create IAM Roles     | IAM           |
| 7    | Create Cluster       | eksctl        |
| 8    | Verify Cluster       | kubectl       |
| 9    | Deploy NGINX         | kubectl       |
| 10   | Expose Service       | LoadBalancer  |
| 11   | Verify Load Balancer | AWS Console   |
| 12   | Cleanup              | eksctl        |

---

## 🧠 Notes

* Use region `us-east-1` for simplicity.
* Load Balancer creation may take **2–4 minutes**.
* Ensure EC2 has permissions via IAM Role or configured credentials.

---

