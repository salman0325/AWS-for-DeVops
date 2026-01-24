: Create ECR Repository**

1. AWS Console → **ECR → Repositories → Create repository**
2. Name: `my-ecr` (example)
3. Visibility: Private
4. Leave defaults → **Create**
5. Note the **repository URI**:

```
332991881817.dkr.ecr.us-east-2.amazonaws.com/my-ecr
```

---

## **Step 2: Build and Push Docker Image**

1. Local machine pe folder with `Dockerfile`
2. Build image:

```bash
docker build -t my-ecr .
```

3. Tag image for ECR:

```bash
docker tag my-ecr:latest 332991881817.dkr.ecr.us-east-2.amazonaws.com/my-ecr:latest
```

4. Login to ECR:

```bash
aws ecr get-login-password --region us-east-2 | docker login --username AWS --password-stdin 332991881817.dkr.ecr.us-east-2.amazonaws.com
```

5. Push image:

```bash
docker push 332991881817.dkr.ecr.us-east-2.amazonaws.com/my-ecr:latest
```

✅ Image ab ECR me hai.

---

## **Step 3: Create ECS Cluster**

1. AWS Console → **ECS → Clusters → Create Cluster**
2. Choose **Networking only (Fargate)**
3. Cluster name: `my-cluster`
4. Leave defaults → **Create**

---

## **Step 4: Create Task Definition**

1. ECS → **Task Definitions → Create new Task Definition → Fargate**
2. Task name: `my-task-defi`
3. Task role: `ecsTaskExecutionRole`
4. Task CPU: `1024` (1 vCPU)
5. Memory: `3072 MiB` (3 GiB)
6. Network mode: `awsvpc`
7. Container definition:

   * Container name: `my-glue-green-app`
   * Image: `332991881817.dkr.ecr.us-east-2.amazonaws.com/my-ecr:latest`
   * Port mappings:

     * Container port: `80`
     * Host port: `80`
     * Protocol: `tcp`
   * App protocol: `http`

---

## **Step 5: Create Service**

1. ECS → Cluster → **Services → Create**
2. Launch type: `Fargate`
3. Task Definition: `my-task-defi`
4. Service name: `my-task-defi-service`
5. Number of tasks: 1
6. Networking:

   * VPC: Select your VPC
   * Subnets: choose **public subnets** (Auto-assign public IP = ENABLED)
   * Security group: create or select SG with **Inbound rule TCP 80 from 0.0.0.0/0**
7. Load balancer: None (for now, optional later)
8. Create service

---

## **Step 6: Find Public IP**

1. ECS → Cluster → Service → Tasks → Running Task
2. Click Task → **Network section**
3. Public IPv4 → copy it
4. Browser me open:

```
http://<Public IPv4>
```

✅ Agar sab sahi → tumhara app browser me open hoga.

---

## **Step 7 (Optional but Recommended): Use ALB**

* Agar multiple AZ ya tasks ho → create **Application Load Balancer**
* Attach ECS service → fixed DNS name
* Security group inbound 80 → ALB
* Public IP ka tension khatam

---

