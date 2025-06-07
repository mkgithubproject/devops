# Running Terraform in Docker - Explained

This guide explains how to use a shell script to run Terraform inside a Docker container. This approach helps isolate the Terraform environment from your host machine, ensuring consistency across different systems.

## `terraform.sh` Script Breakdown

```bash
#!/bin/bash
```

This line specifies that the script should be run using the Bash shell.

```bash
docker run --rm -it \                    # a separate container from your app container
```

* `docker run`: Starts a Docker container.
* `--rm`: Automatically removes the container once it exits.
* `-it`: Allocates a pseudo-TTY and keeps STDIN open for interaction.
* The comment explains that this is not your application container, but a dedicated one for Terraform.

```bash
  -v $(pwd)/terraform:/workspace \
```

* `-v $(pwd)/terraform:/workspace`: Mounts the `terraform` directory from your current path into the container at `/workspace`.

```bash
  -w /workspace \
```

* `-w /workspace`: Sets the working directory inside the container to `/workspace`.

```bash
  -e AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID \
  -e AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY \
```

* These lines pass AWS credentials from your host environment to the container for Terraform to authenticate with AWS.

```bash
  hashicorp/terraform:1.7 $@
```

* Runs the `hashicorp/terraform` Docker image with version 1.7.
* `$@`: Passes all command-line arguments provided to the script directly to the `terraform` command inside the container.

---

## Making the Script Executable

```bash
chmod +x terraform.sh
```

Gives execute permission to the script so it can be run like a regular command.

---

## Running Terraform Commands

### 1. Initialize Terraform

```bash
./terraform.sh init
```

* Runs `terraform init` inside the container.
* Initializes Terraform configuration in the `/workspace` directory.

### 2. Plan with Variables

```bash
./terraform.sh plan -var="key_name=your-key" -var="private_key_path=/workspace/your-key.pem"
```

* Runs `terraform plan` inside the container.
* Sets the `key_name` and `private_key_path` variables inline.
* `/workspace/your-key.pem` refers to the mounted directory path inside the container.

### 3. Apply with Variables

```bash
./terraform.sh apply -var="key_name=your-key" -var="private_key_path=/workspace/your-key.pem"
```

* Runs `terraform apply` with the same variables to provision infrastructure.

> **Note**: Replace `your-key` and `your-key.pem` with actual values you use for SSH key pairs in AWS.

---

## Folder Structure Example

```
project-root/
├── terraform.sh
└── terraform/
    ├── main.tf
    └── your-key.pem
```

---

## Summary

This script enables you to use Terraform within a Docker container, keeping your host system clean and consistent. It's ideal for CI/CD pipelines or for working across different environments without version mismatches.
