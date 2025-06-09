# Docker vs Kubernetes: Explanation with Example

## 🐳 Docker: Containerization Tool

**Docker** is a platform that allows developers to package applications and their dependencies into **containers** — lightweight, portable, and consistent environments.

### 🔧 Key Features:

* Package code + dependencies into a container image
* Run the same container anywhere (local, staging, production)
* Isolate applications
* Manage container lifecycle (start, stop, restart)

### ✅ Good for:

* Developing and running single-container applications
* Ensuring consistency between environments
* Quick local dev & test setups

---

## ☸️ Kubernetes: Container Orchestration Platform

**Kubernetes (K8s)** is a platform for **automating deployment, scaling, and management** of containerized applications.

It does not replace Docker — it **orchestrates** many Docker containers across a cluster of machines.

### 🔧 Key Features:

* Deploy and manage **multi-container applications**
* Auto-healing: restart failed containers
* Load balancing and service discovery
* Auto-scaling based on CPU/memory
* Rollouts & rollbacks of deployments
* Works across multiple nodes (servers)

---

## 🧑‍💻 Example: Node.js To-Do App

### 🐳 Using Docker (Only):

* You create a `Dockerfile`
* Build image: `docker build -t todo-app .`
* Run: `docker run -p 3000:3000 todo-app`
* You manage availability, scaling, failures manually

### ☸️ Using Kubernetes:

* Define `Deployment` YAML to run multiple replicas
* Define a `Service` YAML to expose the app
* Kubernetes runs the app in a cluster (can auto-scale, self-heal, etc.)

#### Deployment Example:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: todo-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: todo
  template:
    metadata:
      labels:
        app: todo
    spec:
      containers:
      - name: todo
        image: yourusername/todo-app:latest
        ports:
        - containerPort: 3000
```

#### Service Example:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: todo-service
spec:
  selector:
    app: todo
  ports:
    - protocol: TCP
      port: 80
      targetPort: 3000
  type: LoadBalancer
```

---

## 🚀 Why Do We Need Kubernetes Over Docker?

| Feature                         | Docker (only)      | Kubernetes                  |
| ------------------------------- | ------------------ | --------------------------- |
| Single container app            | ✅ Yes              | ⚠️ Overkill                 |
| Multi-container orchestration   | ❌ Manual           | ✅ Native support            |
| Load balancing                  | ❌ Manual config    | ✅ Built-in                  |
| Auto-scaling                    | ❌                  | ✅ Yes                       |
| Fault tolerance/self-healing    | ❌ Restart manually | ✅ Auto-restart & reschedule |
| Rolling deployments/rollbacks   | ❌ Complex          | ✅ Built-in                  |
| Cluster management (multi-node) | ❌                  | ✅ Yes                       |

---

## 🧠 Summary

* **Docker** = Package and run a single container.
* **Kubernetes** = Run, scale, and manage **many** containers (often across multiple machines).

In modern DevOps workflows, it's common to:

* Use **Docker** to build container images
* Use **Kubernetes** to deploy and manage those containers in production

---

## Next Steps

Would you like a full walkthrough of deploying a Dockerized app to Kubernetes (e.g., on AWS EKS or Minikube)?

