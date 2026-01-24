
### **1. What is AWS ECR?**

**Answer:**
AWS ECR (Elastic Container Registry) is a fully managed Docker container registry that stores, manages, and deploys container images securely.

---

### **2. What is AWS ECS?**

**Answer:**
AWS ECS (Elastic Container Service) is a fully managed container orchestration service that helps you run, stop, and manage Docker containers on AWS.

---

### **3. What is the difference between ECS and EKS?**

**Answer:**

* **ECS** is AWS’s own container orchestration service.
* **EKS** is based on Kubernetes, an open-source orchestration platform.

---

### **4. What are the main components of ECS?**

**Answer:**

* **Task Definition** – Blueprint of the container (image, CPU, memory, etc.)
* **Task** – Running instance of a task definition
* **Service** – Manages long-running tasks
* **Cluster** – Logical grouping of resources

---

### **5. How do you push a Docker image to ECR?**

**Answer:**

1. Authenticate to ECR
2. Build the Docker image
3. Tag the image
4. Push the image to the ECR repository using `docker push`

---

## 🧠 **Scenario-Based Questions (Practical Use Cases)**

### **6. Scenario:** Your ECS task is failing to pull an image from ECR. What would you check?

**Answer:**

* Check if the **ECR repository policy** allows ECS access.
* Ensure **IAM role permissions** (`AmazonECSTaskExecutionRolePolicy`) are attached.
* Verify the image **URI** is correct.

---

### **7. Scenario:** You updated a container image in ECR, but ECS is still running the old version. Why?

**Answer:**
ECS does not automatically pull the latest image.
You must **update the task definition with the new image tag** and **redeploy** the service.

---

### **8. Scenario:** You want to store private container images securely. How does ECR help?

**Answer:**
ECR provides **private repositories**, **encryption at rest (KMS)**, and **IAM-based access control** to ensure only authorized users or services can pull images.

---

### **9. Scenario:** Your ECS service needs to run on Fargate without managing servers. What should you do?

**Answer:**
Select **Launch Type = Fargate** when creating the ECS service, and specify CPU/memory in the **task definition**.

---

### **10. Scenario:** You have multiple environments (dev, test, prod) using the same ECR image. How do you manage versions?

**Answer:**
Use **image tags** (like `app:dev`, `app:test`, `app:prod`) or **version numbers** (`v1`, `v2`) to manage different deployments easily.

