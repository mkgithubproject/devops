# 🐳 Docker Overview

Docker is an open-source platform that allows developers to automate the deployment of applications inside lightweight, portable containers.

---

## 🔍 In Simple Terms

Docker lets you package your application with everything it needs (code, runtime, libraries, dependencies) into a container so it can run consistently on any system — whether it's your laptop, a server, or the cloud.

---

## 🧱 Key Concepts

| Concept       | Description                                                |
|---------------|------------------------------------------------------------|
| **Image**     | A snapshot/template of a container. It’s a read-only blueprint (like a class). |
| **Container** | A running instance of an image (like an object from a class). |
| **Dockerfile**| A script that defines how to build a Docker image.         |
| **Docker Hub**| A public registry where Docker images are stored and shared. |
| **Docker Engine** | The runtime that runs and manages containers on your system. |

---

## 🎯 Why Use Docker?

✅ **Consistency**: "It works on my machine" becomes "It works everywhere".

🚀 **Speed**: Fast deployments and testing.

🔄 **Isolation**: Avoid dependency conflicts by isolating apps.

📦 **Portability**: Run containers on any OS that supports Docker.

---

## 🖥️ Container vs Virtual Machine (VM)

| Feature              | Container                               | Virtual Machine                          |
|----------------------|------------------------------------------|-------------------------------------------|
| **OS kernel**         | Shared with host                        | Separate full OS                          |
| **Size**              | Very small (10–100MB)                   | Large (GBs)                               |
| **Startup time**      | Seconds                                 | Minutes                                   |
| **Performance**       | Near-native                             | Slower (hardware-level emulation)         |
| **Example base image**| `alpine`, `ubuntu`, `node`, etc.        | Full Ubuntu ISO or Windows image          |


## 💡 Example Use Case

You build a Node.js app and want to ensure it runs the same way on your laptop, a test server, and AWS. You create a Docker image, push it to Docker Hub, and then run containers anywhere.


## 🔐 Handling `.env` Files in Docker

If you're using a `.env` file (e.g., for `PORT=3000` or database credentials), the best practice is to:

- ✅ **Exclude** it from the Docker image using `.dockerignore`
- ✅ **Load** it at runtime using the `--env-file` flag

---

### 📁 1. Add `.env` to `.dockerignore`

Create or update a `.dockerignore` file in your app's root (`todo-api/`) and add:


This prevents the `.env` file from being copied into the image, keeping secrets secure and the image smaller.

---

### 🛠️ 2. Run Docker Container with `.env`

Use the `--env-file` option when running the container to inject environment variables:

```bash
docker run --env-file .env -p 3000:3000 todo-api


---
