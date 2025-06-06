# 📦 Terraform EC2 Instance Provisioning (AWS)

This document explains how the `main.tf` configuration creates and configures an EC2 instance on AWS using Terraform.

---

## 🔧 `main.tf` Breakdown

```hcl
provider "aws" {
  region = "ap-south-1"
}
```

**🔹 Purpose:**
Specifies the AWS provider and sets the region to `ap-south-1` (Mumbai).

---

```hcl
resource "aws_instance" "app" {
  ami           = "ami-0fc5d935ebf8bc3bc"
  instance_type = "t2.micro"
  key_name      = var.key_name
```

**🔹 Purpose:**
Creates an EC2 instance:

* `ami`: Ubuntu base image (replace with latest).
* `instance_type`: Small instance (eligible for free-tier).
* `key_name`: The name of your AWS EC2 Key Pair, passed via variable.

---

```hcl
  tags = {
    Name = "todo-api-server"
  }
```

**🔹 Purpose:**
Adds a name tag to help identify this EC2 instance in the AWS dashboard.

---

### ⚙️ Remote Exec Provisioner

```hcl
  provisioner "remote-exec" {
    inline = ["sudo apt update"]
```

**🔹 Purpose:**
Executes shell commands **after the instance is created**.
In this case, it updates the package list on the Ubuntu instance.

---

### 🔐 SSH Connection Block

```hcl
    connection {
      type        = "ssh"
      user        = "ubuntu"
      private_key = file(var.private_key_path)
      host        = self.public_ip
    }
  }
}
```

**🔹 Purpose:**
Specifies how to connect to the EC2 instance via SSH:

* `user`: Default Ubuntu user.
* `private_key`: Reads your `.pem` key file from a path passed as a variable.
* `host`: Uses the instance's public IP.

---

## 📄 `variables.tf` Example

```hcl
variable "key_name" {
  description = "AWS EC2 Key Pair name"
}

variable "private_key_path" {
  description = "Path to the private key file (.pem)"
}
```

---

## 📄 `outputs.tf` Example

```hcl
output "public_ip" {
  value = aws_instance.app.public_ip
}
```

**🔹 Purpose:**
Displays the EC2 instance's public IP after deployment.

---

## ✅ Summary

| Element         | Description                         |
| --------------- | ----------------------------------- |
| `provider`      | Specifies AWS and region            |
| `resource`      | Creates EC2 instance                |
| `ami`           | Ubuntu image ID                     |
| `instance_type` | Defines size (e.g., `t2.micro`)     |
| `key_name`      | SSH key for access                  |
| `tags`          | Adds resource labels                |
| `provisioner`   | Runs post-deployment shell commands |
| `connection`    | SSH connection setup                |
| `outputs`       | Displays info like public IP        |

---

## 🚀 Next Steps

Once this EC2 instance is created, you can:

* Use **Ansible** to configure the server (e.g., install Node.js, PM2, Nginx)
* Deploy your Node.js app
* Use **Nginx + SSL** for secure web access

> ℹ️ Always keep your `.pem` private key secure and **never commit it** to GitHub!
