# ⚔️ Terraform vs Ansible

## 🧰 Tool Overview

| Tool         | Purpose                                         | Type                           |
|--------------|--------------------------------------------------|--------------------------------|
| **Terraform** | Provision infrastructure (e.g., servers, networks) | Infrastructure as Code (IaC)   |
| **Ansible**   | Configure servers, install software              | Configuration Management        |

---

## 🛠️ Use Case Comparison

| Task                                      | Terraform | Ansible                          |
|-------------------------------------------|-----------|----------------------------------|
| Create AWS EC2 instances                  | ✅ Yes    | 🚫 No (only configures after EC2 exists) |
| Set up VPCs, subnets, load balancers      | ✅ Yes    | 🚫 Not designed for this         |
| Install Nginx, Docker, Node.js on EC2     | 🚫 No     | ✅ Yes                           |
| Push app code to server                   | 🚫 No     | ✅ Yes                           |
| Provision S3, RDS, etc.                   | ✅ Yes    | 🚫 No                            |
| Idempotency (repeatable outcomes)         | ✅ Yes    | ✅ Yes                           |
| Agentless (runs over SSH)                 | ✅ Yes    | ✅ Yes                           |

---

## 🧠 Think of It Like This

> **Terraform** = 🏗️ *Cloud infrastructure builder*  
> **Ansible** = 🔧 *Software installer/configurator on those servers*

---

## 🧪 Real-World Example

### ✅ Use Terraform to:
- Create an EC2 instance  
- Open ports in security groups  
- Attach an EBS volume  

### ✅ Then, use Ansible to:
- SSH into that EC2  
- Install Node.js and PM2  
- Clone your GitHub repo  
- Start your app  

---

## 🔁 When to Use Both Together

In real-world DevOps pipelines, it’s common to combine both:

1. **Terraform** provisions the infrastructure.
2. **Ansible** configures and deploys the application.

> 🚀 This is exactly what you're doing in your Node.js DevOps project!
