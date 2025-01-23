---
title: "DevOps CI/CD GitLab"
mathjax: true
toc: true
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - devOps
  - gitlab
  - CI/CD
---

## Introduction

GitLab is a powerful DevOps platform that provides a complete CI/CD pipeline for automating the build, test, and deployment of applications. In this guide, we'll walk you through the steps to install **GitLab** on **macOS** using **Docker** and configure it for CI/CD workflows.

---

## Prerequisites

Before proceeding, ensure you have the following:

- **macOS** machine with an internet connection.
- **Docker** installed on your system. If not, download it from [Docker](https://www.docker.com/products/docker-desktop) and install it.
- **GitLab user credentials** for accessing the GitLab UI.

---

## Step 1: Install Docker

First, ensure Docker is installed on your **macOS** system. If you haven’t done it yet, download Docker Desktop for macOS from the [official website](https://www.docker.com/products/docker-desktop). Once downloaded, follow the installation steps.

To verify the installation, run:

```bash
docker --version
```

This should return the Docker version installed.

---

## Step 2: Pull the GitLab Docker Image

To install **GitLab** using Docker, run the following command to pull the official GitLab Community Edition image:

```bash
docker pull gitlab/gitlab-ce:latest
```

---

## Step 3: Run GitLab Using Docker

Once the image is downloaded, you can run GitLab in a Docker container. Execute the following command:

```bash
docker run -d -p 80:80 -p 443:443 -p 22:22 --name gitlab --restart always \
-v /your/host/path/to/gitlab/config:/etc/gitlab \
-v /your/host/path/to/gitlab/data:/var/opt/gitlab \
-v /your/host/path/to/gitlab/logs:/var/log/gitlab \
gitlab/gitlab-ce:latest
```

### Explanation:
- `-p 80:80 -p 443:443 -p 22:22`: Maps the required ports for HTTP, HTTPS, and SSH.
- `--name gitlab`: Names the container `gitlab`.
- `--restart always`: Ensures GitLab restarts automatically if the container or Docker daemon restarts.
- `-v /your/host/path`: Mounts host directories to the container for persistent data.

Replace `/your/host/path` with the actual paths on your machine where GitLab data, logs, and configuration will be stored.

---

## Step 4: Access GitLab Web Interface

Once GitLab is running, open your web browser and navigate to:

```
http://localhost
```

You should see the GitLab setup page. Follow the instructions to set up an admin password and configure your GitLab instance.

---

## Step 5: Configure GitLab for CI/CD

GitLab offers built-in CI/CD features to automate the build, test, and deployment of your code. To configure GitLab CI/CD:

1. **Create a `.gitlab-ci.yml` file** in the root of your repository. This file defines the CI/CD pipeline for your project.

Example `.gitlab-ci.yml` for a simple Node.js application:

```yaml
stages:
  - build
  - test
  - deploy

build:
  stage: build
  script:
    - npm install

test:
  stage: test
  script:
    - npm test

deploy:
  stage: deploy
  script:
    - ./deploy.sh
```

2. **Push your repository to GitLab**. GitLab will automatically detect the `.gitlab-ci.yml` file and trigger the pipeline based on the defined stages.

---

## Step 6: Monitor GitLab Logs

To monitor the logs of the GitLab container, you can use the following command:

```bash
docker logs -f gitlab
```

This will show real-time logs for the GitLab container.

---

## Step 7: Automating GitLab Restart (Optional)

To ensure GitLab restarts automatically after a system reboot, you can set up Docker's restart policy. Run the following command:

```bash
docker update --restart unless-stopped gitlab
```

---

## Step 8: Backup GitLab (Optional)

It's important to back up your GitLab data regularly. Follow these steps to back it up:

1. **Stop GitLab container**:

   ```bash
   docker stop gitlab
   ```

2. **Backup GitLab data**:

   ```bash
   docker cp gitlab:/var/opt/gitlab /path/to/gitlab/backup/folder
   ```

3. **Restart GitLab container**:

   ```bash
   docker start gitlab
   ```

---

## Conclusion

By following this guide, you have successfully installed **GitLab** on **macOS** using Docker and set up **CI/CD pipelines** for your projects. GitLab makes it easy to automate your build, test, and deployment processes.

Let me know if you need further assistance!