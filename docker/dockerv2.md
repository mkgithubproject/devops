Here’s a **comprehensive guide to Docker interview questions** with **simple, real-world explanations** tailored for backend developers like you (especially for Node.js + DevOps stack at mid-level roles like Infosys, 12+ LPA target).

---

## 🚢 What is Docker?

Docker is a **containerization platform** that allows you to package your application with all its dependencies into a **container** so it can run reliably anywhere (dev, test, prod).

> Think of it like a **lightweight virtual machine** just for your app, but faster and more efficient.

---

## 🔥 Common Docker Interview Questions with Answers

---

### 1. **What is Docker and why is it used?**

**Answer:**
Docker packages applications and dependencies into **containers** to ensure consistency across environments (local → staging → production).

✅ **Why used?**

* Environment consistency
* Easy CI/CD integration
* Faster deployment
* Lightweight vs VMs

---

### 2. **What is the difference between a Docker container and a virtual machine (VM)?**

| Feature        | Docker Container      | Virtual Machine              |
| -------------- | --------------------- | ---------------------------- |
| OS Layer       | Shares host OS kernel | Has full guest OS            |
| Startup Time   | Seconds               | Minutes                      |
| Resource Usage | Lightweight (MBs)     | Heavy (GBs)                  |
| Isolation      | Process-level         | Full machine-level isolation |

---

### 3. **What is a Dockerfile?**

**Answer:**
A `Dockerfile` is a **script with instructions** to build a Docker image.

Example `Dockerfile` for a Node.js app:

```Dockerfile
FROM node:18
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
CMD ["node", "index.js"]
```

---

### 4. **What is the difference between an image and a container?**

| Term      | Description                                |
| --------- | ------------------------------------------ |
| Image     | Blueprint (read-only) to create containers |
| Container | Running instance of an image               |

---

### 5. **How do you build and run a Docker image?**

```bash
# Build an image from Dockerfile
docker build -t my-app .

# Run a container from the image
docker run -p 3000:3000 my-app
```

---

### 6. **What is Docker Compose?**

**Answer:**
Docker Compose is a tool to **define and run multi-container apps** using a single `docker-compose.yml` file.

Example:

```yaml
version: '3'
services:
  app:
    build: .
    ports:
      - "3000:3000"
  mongo:
    image: mongo
    ports:
      - "27017:27017"
```

Run it using:

```bash
docker-compose up
```

---

### 7. **How do volumes work in Docker?**

**Answer:**
Volumes are used to **persist data** across container restarts or rebuilds.

```bash
docker run -v mydata:/app/data my-app
```

Or in Compose:

```yaml
volumes:
  - ./data:/app/data
```

---

### 8. **What are some commonly used Docker commands?**

| Command                          | Purpose                     |
| -------------------------------- | --------------------------- |
| `docker build -t name .`         | Build image from Dockerfile |
| `docker run -p 80:80 image`      | Run container               |
| `docker ps`                      | List running containers     |
| `docker exec -it container bash` | Access container shell      |
| `docker logs container`          | View logs                   |
| `docker stop/start/restart`      | Manage container lifecycle  |

---

### 9. **What is the difference between CMD and ENTRYPOINT in a Dockerfile?**

| CMD                    | ENTRYPOINT                          |
| ---------------------- | ----------------------------------- |
| Default command to run | Always runs, even if CMD overridden |
| Can be overridden      | Hard to override                    |

Example:

```Dockerfile
CMD ["npm", "start"]
ENTRYPOINT ["node", "server.js"]
```

---

### 10. **How do you reduce Docker image size?**

✅ Tips:

* Use a **smaller base image** (e.g., `node:alpine`)
* Use `.dockerignore` (like `.gitignore`)
* Combine commands to reduce layers
* Remove unnecessary files

---

### 11. **What is a multi-stage build in Docker?**

**Answer:**
Multi-stage builds allow you to **build the app in one image** and **copy only the needed files into the final image** (keeping it small).

Example:

```Dockerfile
# Stage 1 - build
FROM node:18 AS builder
WORKDIR /app
COPY . .
RUN npm install && npm run build

# Stage 2 - production
FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
CMD ["node", "dist/index.js"]
```

---

### 12. **How do you handle environment variables in Docker?**

**Answer:**
You can pass env variables in multiple ways:

```bash
docker run -e NODE_ENV=production my-app
```

Or use `.env` file in `docker-compose.yml`:

```yaml
environment:
  - DB_URL=${DB_URL}
```

---

### 13. **What happens when a Docker container exits?**

* It **stops running**, but remains unless `--rm` is used.
* You can restart it:

```bash
docker start <container_id>
```

---

### 14. **How do you debug a Docker container?**

✅ Steps:

* Use `docker logs <container>`
* Use `docker exec -it <container> bash`
* Use health checks in `docker-compose.yml`

---

### 15. **What are common use cases of Docker in real projects?**

* Run isolated services (like Node.js app, MongoDB)
* Simplify CI/CD (GitHub Actions + Docker)
* Create consistent dev environments
* Package microservices

---

## 🚀 BONUS: Docker + Node.js Microservice Example

Let me know if you'd like a full **Node.js + MongoDB + Docker Compose microservice setup**, and I’ll send you a ready-to-run project.

---

## ✅ Want to go further?

Would you like:

1. Docker + Kubernetes interview Q\&A?
2. Real Dockerfile & Compose file for your Node.js project?
3. GitHub Actions + Docker CI/CD pipeline explanation?

Let me know your priority and I’ll guide you next.
