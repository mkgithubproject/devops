# 🚀 DevOps Project: Node.js To-Do API with Dockerized Terraform & Ansible

This guide walks you through building and deploying a **Node.js To-Do REST API** using a complete DevOps stack with **Terraform inside Docker**, **Ansible**, **Docker**, and **GitHub Actions**.

---

## 📁 Folder Structure

```
📦todo-devops
├── 📂.github
│   └── 📂workflows
│       └── main.yml           # GitHub Actions pipeline
├── 📂ansible
│   ├── inventory              # Ansible inventory file
│   └── setup.yml              # Ansible playbook
├── 📂terraform
│   ├── main.tf                # Terraform AWS setup
│   ├── variables.tf           # Terraform variables
│   └── outputs.tf             # Terraform outputs
├── 📂todo-api
│   ├── index.js               # Express app
│   ├── package.json
│   ├── .env
│   └── Dockerfile
├── .gitignore
├── terraform.sh              # Helper script for Dockerized Terraform
└── README.md
```

---

## 🔧 Phase 1: Node.js To-Do API Setup

### `todo-api/index.js`

```js
const express = require('express');
const app = express();
const PORT = process.env.PORT || 3000;

let todos = [];

app.use(express.json());

app.get('/todos', (req, res) => res.json(todos));
app.post('/todos', (req, res) => {
  const todo = { id: Date.now(), task: req.body.task };
  todos.push(todo);
  res.status(201).json(todo);
});

app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
```

### `.env`

```
PORT=3000
```

### `Dockerfile`

```dockerfile
FROM node:18-alpine               # Starts with a lightweight Node.js 18 base image built on Alpine Linux (fast and small).
WORKDIR /app                     # Sets the working directory inside the container to /app. All subsequent commands (like COPY, RUN, CMD)                                           happen relative to this directory.
COPY . .                          # Copies everything from the current build context (where docker build is run) into the container’s /app                                           folder.
RUN npm install                    # Installs all Node.js dependencies listed in package.json.
CMD ["node", "index.js"]           # The command that runs when a container is started. It launches your API via index.js.
```
## if Dockerfile is not in the root folder 
### Change Build Context & COPY Path
cd todo-api

docker build -f devops_todo/docker/Dockerfile -t todo-api .

### But you also need to modify your Dockerfile like this:

FROM node:18-alpine

WORKDIR /app

COPY ../../ .     # Go two levels up to copy everything from todo-api/

RUN npm install

CMD ["node", "index.js"]

### `.gitignore`
```
node_modules
.env
```

### Initialize Git repo

```bash
cd todo-api
npm init -y
npm install express
cd ..
git init
```

---

## ☁️ Phase 2: Dockerized Terraform for AWS EC2

### `terraform/main.tf`

```hcl
provider "aws" {
  region = "ap-south-1"
}

resource "aws_instance" "app" {
  ami           = "ami-0fc5d935ebf8bc3bc" # Replace with latest Ubuntu AMI
  instance_type = "t2.micro"
  key_name      = var.key_name

  tags = {
    Name = "todo-api-server"
  }

  provisioner "remote-exec" {
    inline = ["sudo apt update"]
    connection {
      type        = "ssh"
      user        = "ubuntu"
      private_key = file(var.private_key_path)
      host        = self.public_ip
    }
  }
}
```

### `terraform/variables.tf`

```hcl
variable "key_name" {}
variable "private_key_path" {}
```

### `terraform/outputs.tf`

```hcl
output "public_ip" {
  value = aws_instance.app.public_ip
}
```

### `terraform.sh` (root folder)

```bash
#!/bin/bash
docker run --rm -it \
  -v $(pwd)/terraform:/workspace \
  -w /workspace \
  -e AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID \
  -e AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY \
  hashicorp/terraform:1.7 $@
```

### Run Terraform

```bash
chmod +x terraform.sh
./terraform.sh init
./terraform.sh plan -var="key_name=your-key" -var="private_key_path=/workspace/your-key.pem"
./terraform.sh apply -var="key_name=your-key" -var="private_key_path=/workspace/your-key.pem"
```

---

## ⚙️ Phase 3: Provision EC2 with Ansible

### `ansible/inventory`

```
[web]
<public-ip> ansible_user=ubuntu ansible_ssh_private_key_file=../your-key.pem
```

### `ansible/setup.yml`

```yaml
- hosts: web
  become: yes
  tasks:
    - name: Install Node.js & Git
      apt:
        name: [nodejs, npm, git]
        state: present
        update_cache: yes

    - name: Install PM2
      npm:
        name: pm2
        global: yes

    - name: Clone repo
      git:
        repo: https://github.com/yourusername/todo-api.git
        dest: /home/ubuntu/todo-api

    - name: Install dependencies
      shell: npm install
      args:
        chdir: /home/ubuntu/todo-api

    - name: Start app
      shell: pm2 start index.js
      args:
        chdir: /home/ubuntu/todo-api
```

### Run Ansible

```bash
cd ansible
ansible-playbook -i inventory setup.yml
```

---

## 🚀 Phase 4: GitHub Actions CI/CD

### `.github/workflows/main.yml`

```yaml
name: CI/CD

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
    - run: npm test || echo "no tests yet"

    - name: Docker Build & Push
      run: |
        docker build -t yourdockerhub/todo-api ./todo-api
        echo ${{ secrets.DOCKER_PASSWORD }} | docker login -u ${{ secrets.DOCKER_USERNAME }} --password-stdin
        docker push yourdockerhub/todo-api
```

---

## 🌐 Phase 5: Nginx + SSL (on EC2)

```bash
sudo apt install nginx
```

### `/etc/nginx/sites-available/default`

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

### Enable HTTPS

```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d yourdomain.com
```

---

## 📊 Phase 6: Monitoring with PM2

```bash
pm2 status
pm2 logs
pm2 startup
pm2 save
```

---

## ✅ Done!

You now have:

* Node.js API with Docker
* Infrastructure provisioned via **Terraform in Docker**
* Configuration with **Ansible**
* CI/CD with **GitHub Actions**
* HTTPS with **Nginx + Let's Encrypt**

> 💡 Tip: Use `tmux` or `screen` when SSHing into servers for long-running sessions.
