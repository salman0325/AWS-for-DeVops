
# AWS EKS Cluster - Granting New User Access

This document explains how to give a new user (intern, developer) access to an existing AWS EKS cluster using best practices. The process covers everything from creating an IAM user to verifying access with `kubectl get pods`.

---

## Prerequisites

- Existing AWS EKS cluster running
- Cluster-admin privileges (you)
- AWS CLI installed
- `kubectl` installed
- Intern/dev AWS IAM user credentials

---

## Step 1: Create a new AWS IAM User

1. Login to AWS Management Console → IAM → Users → Add User
2. Username: `intern-user`
3. Access type: **Programmatic access** (for CLI / kubectl)
4. Click "Next: Permissions" → **skip** for now
5. Create user and **save Access Key ID and Secret Access Key**

---

## Step 2: Add IAM User to EKS Cluster (`aws-auth` ConfigMap)

1. Edit `aws-auth` configmap in `kube-system` namespace:

```bash
kubectl edit configmap aws-auth -n kube-system
````

2. Add new user mapping under `mapUsers`:

```yaml
mapUsers: |
  - userarn: arn:aws:iam::<ACCOUNT_ID>:user/intern-user
    username: intern
    groups:
      - interns
```

> Replace `<ACCOUNT_ID>` with your AWS account ID.

3. Save and exit. The IAM user is now recognized by the cluster.

---

## Step 3: Create a Read-Only Role in Kubernetes

1. Create a Role with read-only access to pods in `default` namespace:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
```

2. Apply the role:

```bash
kubectl apply -f pod-reader-role.yaml
```

---

## Step 4: Bind the Role to the New User

1. Create a RoleBinding:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: intern-pod-reader-binding
  namespace: default
subjects:
- kind: User
  name: intern   # matches username in aws-auth
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

2. Apply the binding:

```bash
kubectl apply -f intern-rolebinding.yaml
```

---

## Step 5: Intern Setup on Local Machine

1. Install AWS CLI and `kubectl`
2. Configure AWS credentials for intern user:

```bash
aws configure
# Enter Access Key ID and Secret Access Key
# Set default region (e.g., us-east-2)
```

3. Update kubeconfig to connect to cluster:

```bash
aws eks update-kubeconfig --name my-eks-cluster --region us-east-2 --profile intern-user
```

---

## Step 6: Verify Access

1. Check pods in `default` namespace:

```bash
kubectl get pods
```

* Expected: list of pods visible
* Example:

```
NAME          READY   STATUS    RESTARTS   AGE
my-continer   1/1     Running   0          10m
```

2. Attempt restricted actions (optional):

```bash
kubectl delete pod my-continer
```

* Should return: `Error from server (Forbidden): ...` ✅ confirms read-only permissions

---

## Notes / Best Practices

* **Never share cluster-admin credentials**
* Use **IAM users + RBAC roles** for controlled access
* Assign namespace-specific or resource-specific permissions
* For multiple interns/devs, repeat steps 1–5 with unique IAM users

---

This process allows **new users to safely access the cluster** and view pods without risking production workloads.

```
