# GitHub Actions (`.github/workflows/main.yml`) vs Jenkins

This document provides a comprehensive comparison between GitHub Actions and Jenkins for CI/CD workflows, followed by practical examples of implementing the same pipeline in both systems.

---

## Overview Comparison

| Feature             | GitHub Actions (`main.yml`)                             | Jenkins                                            |
| ------------------- | ------------------------------------------------------- | -------------------------------------------------- |
| **Purpose**         | Define CI/CD pipelines using YAML in GitHub repository. | Run CI/CD pipelines via Jenkins server.            |
| **Trigger**         | GitHub events (push, pull request, schedule, etc.)      | GitHub webhooks or polling.                        |
| **Setup**           | Fully managed by GitHub.                                | Requires self-hosted server or cloud installation. |
| **Pipeline Syntax** | YAML                                                    | Groovy (Jenkinsfile) or UI-based freestyle jobs.   |
| **Extensibility**   | GitHub Actions Marketplace (JavaScript/Docker actions)  | Thousands of plugins (Groovy, Java).               |
| **Security**        | GitHub-managed, sandboxed.                              | Self-managed, security is your responsibility.     |
| **Cost**            | Free tier available, then paid based on minutes         | Jenkins is free, infrastructure costs apply.       |
| **Best For**        | Simple to medium CI/CD needs integrated with GitHub     | Complex, custom, enterprise-grade CI/CD pipelines  |

---

## Example Use Case

A Node.js project pipeline with the following stages:

* Checkout code
* Install dependencies
* Run tests
* Build project
* Deploy (mock)

---

## GitHub Actions Example

**File:** `.github/workflows/main.yml`

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'

      - name: Install dependencies
        run: npm install

      - name: Run tests
        run: npm test

      - name: Build project
        run: npm run build

      - name: Deploy (mock)
        run: echo "Deploying to production..."
```

### Highlights

* Simple YAML-based syntax
* Uses GitHub-hosted runners
* Integrated tightly with GitHub

---

## Jenkins Example

**File:** `Jenkinsfile`

```groovy
pipeline {
    agent any

    environment {
        NODE_VERSION = '18'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Set up Node') {
            steps {
                sh 'nvm install $NODE_VERSION'
                sh 'nvm use $NODE_VERSION'
            }
        }

        stage('Install dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run tests') {
            steps {
                sh 'npm test'
            }
        }

        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying to production...'
            }
        }
    }
}
```

### Highlights

* Uses declarative Groovy syntax
* Requires Jenkins server with appropriate plugins/tools
* Provides more flexibility and plugin support for complex pipelines

---

## Conclusion

| Use Case                           | Recommended Tool |
| ---------------------------------- | ---------------- |
| Quick setup for GitHub projects    | GitHub Actions   |
| Custom on-premise environments     | Jenkins          |
| Lightweight, low-maintenance CI/CD | GitHub Actions   |
| Complex enterprise workflows       | Jenkins          |

---

## Next Steps

Would you like to expand this comparison to include Docker builds, Terraform infrastructure deployment, or AWS integration examples?
