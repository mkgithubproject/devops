# DevOps Project: Zero to Hero with Node.js, Terraform, and Ansible

## ✨ Project Overview

A complete DevOps pipeline for deploying a Node.js REST API using:

* **Node.js + Express**: RESTful To-Do API
* **GitHub**: Source control and CI/CD
* **Terraform**: Infrastructure provisioning (AWS EC2, security groups, etc.)
* **Ansible**: Server configuration and app deployment
* **Docker**: Containerization
* **GitHub Actions**: CI/CD pipeline
* **Nginx + SSL**: Reverse proxy and HTTPS setup
* **PM2**: Node.js process management

---

## 🚧 Phase Plan

| Phase               | Description                                       |
| ------------------- | ------------------------------------------------- |
| 📁 1. Project Setup | Create Node.js app, push to GitHub                |
| ☁️ 2. Terraform     | Provision AWS EC2 and networking                  |
| ⚙️ 3. Ansible       | Configure EC2: install Node.js, Docker, app setup |
| 🐳 4. Docker        | Containerize app for consistent environment       |
| 🚀 5. CI/CD         | GitHub Actions: test, build Docker image, deploy  |
| 🌐 6. Nginx + SSL   | Reverse proxy + HTTPS with Let's Encrypt          |
| 📊 7. Monitoring    | PM2 logs, optional Prometheus/Grafana             |

---

## 🛠️ Tools and Technologies

| Tool                  | Role                                  |
| --------------------- | ------------------------------------- |
| **Node.js + Express** | Backend application                   |
| **Git + GitHub**      | Version control and CI/CD platform    |
| **Docker**            | Build and run app in container        |
| **Terraform**         | Create cloud infrastructure on AWS    |
| **Ansible**           | Configure EC2 instance and deploy app |
| **GitHub Actions**    | CI/CD pipeline automation             |
| **Nginx**             | Reverse proxy and SSL termination     |
| **PM2**               | Node.js production process manager    |

---

## 🔄 Phase 1: Node.js REST API Setup

1. Create a folder:

   ```bash
   mkdir todo-api && cd todo-api
   npm init -y
   npm install express mongoose dotenv
   ```

2. Create basic app:

   ```js
   // index.js
   const express = require('express');
   const app = express();
   const PORT = process.env.PORT || 3000;

   app.use(express.json());

   app.get('/', (req, res) => res.send('Hello World'));

   app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
   ```

3. Create `.env`, `.gitignore`, and `Dockerfile` (in Phase 4)

4. Initialize Git repo:

   ```bash
   git init
   git remote add origin https://github.com/yourusername/todo-api.git
   git add . && git commit -m "initial commit"
   git push -u origin main
   ```

---

## ☁️ Phase 2: Infrastructure with Terraform

1. Install Terraform
2. Create `main.tf`, `variables.tf`, `outputs.tf`
3. Configure AWS provider and resources: in main.tf

   * VPC (optional)
   * Security Group
   * EC2 instance

```hcl
provider "aws" {
  region = "ap-south-1"
}

resource "aws_instance" "web" {
  ami           = "ami-xxxxxxx"
  instance_type = "t2.micro"
  key_name      = var.key_name
  ...
}
```

Run Terraform:

```bash
terraform init
terraform plan
terraform apply
```

---

## ⚙️ Phase 3: Configuration with Ansible

1. Create inventory file:

```ini
[web]
<your-ec2-public-ip> ansible_user=ubuntu
```

2. Playbook (`setup.yml`) example:

```yaml
- hosts: web
  become: yes
  tasks:
    - name: Install Node.js
      apt:
        name: nodejs
        state: present
    - name: Clone repo
      git:
        repo: https://github.com/yourusername/todo-api.git
        dest: /home/ubuntu/app
    - name: Start app with PM2
      shell: pm2 start index.js
```

3. Run playbook:

```bash
ansible-playbook -i inventory setup.yml
```

---

## 🐳 Phase 4: Dockerize the App

1. Create `Dockerfile`:

```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY . .
RUN npm install
CMD ["node", "index.js"]
```

2. Build & run:

```bash
docker build -t todo-api .
docker run -p 3000:3000 todo-api
```

3. Push to Docker Hub (optional):

```bash
docker tag todo-api yourdockerhub/todo-api
docker push yourdockerhub/todo-api
```

---

## 🚀 Phase 5: CI/CD with GitHub Actions

1. Create `.github/workflows/main.yml`

```yaml
name: Node.js CI

on:
  push:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v2
        with:
          node-version: 18
      - run: npm install
      - run: npm test
```

2. Extend workflow to:

   * Build Docker image
   * Push to Docker Hub
   * Trigger Ansible playbook via SSH or webhook

---

## 🌐 Phase 6: Nginx + SSL

1. SSH into EC2 and install Nginx

```bash
sudo apt update
sudo apt install nginx
```

2. Configure reverse proxy:

```nginx
server {
  listen 80;
  server_name yourdomain.com;

  location / {
    proxy_pass http://localhost:3000;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
  }
}
```

3. Install SSL (Let's Encrypt):

```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d yourdomain.com
```

---

## 📊 Phase 7: Monitoring & Logging

* Use **PM2** for logs:

```bash
pm2 logs
pm2 status
```

* Optional: Add Prometheus + Grafana for deeper monitoring.

---

## 🙌 Done!

You now have:

* Automated infrastructure setup with **Terraform**
* Automated server configuration with **Ansible**
* Dockerized **Node.js app** with CI/CD via **GitHub Actions**
* Production-ready deployment on **AWS EC2** with **SSL & Nginx**

> 🚀 Ready to scale, monitor, and build real-world DevOps pipelines!
