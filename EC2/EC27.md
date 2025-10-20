

# AWS AMI (Amazon Machine Image) Guide

This document explains what an AMI is, how to create, share, and delete your own AMI in AWS, along with common questions.

---

## What is an AMI?

An **AMI (Amazon Machine Image)** is a template used to launch EC2 instances. It contains the operating system, installed software, and configuration settings.

---

## How to Create an AMI? (Step by Step)

1. **Launch an EC2 Instance**  
   - Go to AWS Console > EC2 Dashboard.  
   - Choose a base AMI (e.g., Amazon Linux).  
   - Launch an instance and install any required software.

2. **Customize the Instance**  
   - SSH into the instance.  
   - Install applications and configure the environment as needed.

3. **Create the AMI**  
   - In EC2 Dashboard, select your instance.  
   - Click Actions > Image and templates > Create image.  
   - Provide a name and description, then click Create Image.

4. **Wait for the AMI Creation to Complete**  
   - Go to EC2 > AMIs.  
   - Wait until the AMI status is **available**.

5. **Launch New Instances Using Your AMI**  
   - Select your custom AMI in EC2 > AMIs.  
   - Click Launch instance from image.  

---

## How to Share an AMI?

### Share with a Specific AWS Account:

- Go to EC2 Dashboard > AMIs.  
- Select your AMI.  
- Click Actions > Modify Image Permissions.  
- Enter the 12-digit AWS Account ID of the user you want to share with.  
- Save changes.  
- **Note:** You must also share the associated snapshots with the same account.

### Make AMI Public (Anyone Can Use):

- Select your AMI.  
- Click Actions > Modify Image Permissions.  
- Choose **Public** visibility.  
- **Warning:** This allows anyone to launch instances using your AMI.

---

## How to Delete an AMI?

1. **Deregister the AMI**  
   - Go to EC2 Dashboard > AMIs.  
   - Select your AMI.  
   - Click Actions > Deregister.  

2. **Delete Associated Snapshots**  
   - Go to EC2 Dashboard > Snapshots.  
   - Find the snapshots linked to your AMI.  
   - Select and delete them to avoid ongoing storage charges.

---

## Frequently Asked Questions (FAQs)

**Q1: What is an AMI?**  
A: An AMI is a template containing OS and software used to launch EC2 instances.

**Q2: How can I share my AMI with someone else?**  
A: Modify the AMI's permissions to add their AWS account ID or make it public.

**Q3: What happens when I delete an AMI?**  
A: The AMI is deregistered, but snapshots remain until deleted separately to avoid charges.

**Q4: Does the AMI include installed software?**  
A: Yes, it captures the entire state of the instance, including installed applications.

---

## Need Help?

Feel free to ask if you have any questions!

---

*Created by [SALMAN-----KHAN]*
```

---

