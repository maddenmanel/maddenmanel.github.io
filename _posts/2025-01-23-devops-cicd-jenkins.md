---
title: "Devops CI/CD Jenkins"
mathjax: true
toc: true
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - devOps
  - jenkins
  - CI/CD
---

## Introduction

Jenkins is a popular open-source automation server that facilitates continuous integration and continuous delivery (CI/CD). It helps automate the building, testing, and deployment of applications. This guide provides detailed steps to install Jenkins on **macOS** using **Docker**, configure a basic pipeline, and introduce approval processes for SQL script execution and release deployment.

Using Docker simplifies the installation process and ensures that Jenkins is isolated in a container, avoiding dependency conflicts with the host system. After installation, we will configure Jenkins for building, testing, and deploying applications while introducing a simple approval process.

---

## Prerequisites

Before proceeding, ensure you have the following:

- **macOS** machine with an internet connection.
- **Docker** installed on your system. If not, download it from [Docker](https://www.docker.com/products/docker-desktop) and install it.
- **Jenkins user credentials** for logging into the Jenkins UI.

---

## Step 1: Install Docker

First, ensure Docker is installed on your **macOS** system. If you haven’t done it yet, download Docker Desktop for macOS from the [official website](https://www.docker.com/products/docker-desktop). Once downloaded, follow the installation steps.

To verify the installation, run:

```bash
docker --version
```

This should return the Docker version installed.

---

## Step 2: Pull the Jenkins Docker Image

Now that Docker is installed, we can pull the official Jenkins image. Open your terminal and run the following command:

```bash
docker pull jenkins/jenkins:lts
```

This will pull the **Long-Term Support (LTS)** version of Jenkins, which is stable and recommended for production environments.

---

## Step 3: Run Jenkins Using Docker

Once the image is downloaded, you can run Jenkins in a Docker container. Execute the following command to run Jenkins:

```bash
docker run -d -p 8080:8080 -p 50000:50000 --name jenkins jenkins/jenkins:lts
```

### Explanation:

- `-d`: Run the container in detached mode.
- `-p 8080:8080`: Map port `8080` on the container to port `8080` on your host, which allows you to access Jenkins UI at `http://localhost:8080`.
- `-p 50000:50000`: Expose the port `50000` for communication between Jenkins and its agents.
- `--name jenkins`: Name the container `jenkins` for easy reference.

---

## Step 4: Access Jenkins Web Interface

To access Jenkins, open your web browser and navigate to:

```
http://localhost:8080
```

On your first visit, Jenkins will ask for an **unlock key**. To retrieve it, execute the following command:

```bash
docker exec -it jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

Copy the output (the password), and paste it into the Jenkins unlock screen to proceed with the initial setup.

---

## Step 5: Set Up Jenkins

Once Jenkins is unlocked, follow the setup wizard:

1. **Install suggested plugins**: Jenkins will suggest a set of commonly used plugins. Click on "Install suggested plugins."
2. **Create an admin user**: After plugins are installed, create a user with admin privileges by following the prompts. This is your primary user for accessing Jenkins.
3. **Jenkins Setup Complete**: After creating the admin user, Jenkins will show a "Setup is Complete" message.

---

## Step 6: Configure Jenkins Pipeline with SQL and Release Approval

Jenkins allows you to automate tasks through **Pipelines**, which define your build, test, and deployment workflows. To get started, we’ll configure a simple pipeline and introduce an **approval process** before executing SQL scripts and releasing deployments.

### a) Install Required Plugins

To enable pipeline support and approval steps, you need the following plugins:

1. **Pipeline**: For creating Jenkins pipelines.
2. **Input Step Plugin**: To introduce manual approval in the pipeline.

To install these:

1. Go to `Manage Jenkins` > `Manage Plugins`.
2. Under the **Available** tab, search and install the **Pipeline** and **Input Step Plugin**.

### b) Create a New Pipeline

1. On the Jenkins dashboard, click on **New Item**.
2. Enter a name for your project (e.g., "MyFirstPipeline").
3. Choose **Pipeline** and click **OK**.
4. In the configuration screen, scroll down to the **Pipeline** section and enter the following script:

```groovy
pipeline {
    agent any

    environment {
        // 这里可以设置一些全局变量或常用参数，如数据库连接等
        DB_HOST = 'your_db_host'
        DB_USER = 'your_db_user'
        DB_PASS = 'your_db_password'
    }

    stages {
        stage('Checkout') {
            steps {
                // 检出代码库，假设你使用Git
                checkout scm
            }
        }

        stage('SQL Approval') {
            steps {
                // 在SQL审批阶段进行人工审批，审批人员可以填写SQL审批意见
                script {
                    def approval = input message: 'SQL脚本审批', 
                                          parameters: [
                                              string(defaultValue: '', description: '请输入SQL审批意见', name: 'sql_approval_comments')
                                          ]
                    // 将审批意见存储或使用
                    echo "SQL审批意见: ${approval['sql_approval_comments']}"
                }
            }
        }

        stage('Run SQL Scripts') {
            steps {
                // 在SQL审批通过后执行SQL脚本
                script {
                    echo '执行SQL脚本...'
                    // 这里假设你用sh步骤来执行SQL脚本，也可以使用数据库相关插件
                    sh '''
                    mysql -h ${DB_HOST} -u ${DB_USER} -p${DB_PASS} -e "source ./your_sql_script.sql"
                    '''
                }
            }
        }

        stage('Release Approval') {
            steps {
                // 在上线前进行审批，审批人可以填写上线内容
                script {
                    def releaseApproval = input message: '是否批准上线？',
                                                 parameters: [
                                                     string(defaultValue: '', description: '请输入上线内容', name: 'release_notes'),
                                                     string(defaultValue: '', description: '请输入上线审批意见', name: 'release_approval_comments')
                                                 ]
                    // 获取上线内容和审批意见
                    echo "上线内容: ${releaseApproval['release_notes']}"
                    echo "上线审批意见: ${releaseApproval['release_approval_comments']}"
                }
            }
        }

        stage('Deploy') {
            steps {
                // 上线操作，假设部署应用
                script {
                    echo '执行部署操作...'
                    sh '''
                    ./deploy.sh  # 替换为实际的部署脚本或命令
                    '''
                }
            }
        }
    }

    post {
        always {
            // 这里可以定义清理工作或通知等
            echo 'Pipeline 执行完毕'
        }

        success {
            echo 'Pipeline 执行成功'
        }

        failure {
            echo 'Pipeline 执行失败'
        }
    }
}
```

### c) Save and Run the Pipeline

- After entering the pipeline script, click **Save**.
- Trigger the pipeline by clicking **Build Now**.

The pipeline will:
1. **Checkout** the code from your version control system (assuming it’s linked).
2. **SQL Approval**: Wait for manual approval and SQL comments before executing SQL scripts.
3. **Run SQL Scripts**: Execute SQL scripts only after SQL approval.
4. **Release Approval**: Wait for manual approval of the release, where the release notes and approval comments are entered.
5. **Deploy**: Once all approvals are granted, the deployment will proceed.

---

## Step 7: Monitor Jenkins Logs and Jobs

To monitor Jenkins logs and ensure the server is running smoothly, use the following command:

```bash
docker logs -f jenkins
```

This will display Jenkins logs in real-time. To view the build job logs, go to the **Build History** section in your Jenkins project page.

---

## Step 8: Automating Jenkins Restart (Optional)

To ensure that Jenkins restarts automatically after a system reboot, you can set up Docker's restart policy. Stop the container and run the following command to restart it automatically on failure or reboot:

```bash
docker update --restart unless-stopped jenkins
```

---

## Step 9: Configure Backups (Optional)

It’s important to regularly back up your Jenkins data. To back up your Jenkins instance and its configuration, follow these steps:

1. **Stop the Jenkins container**:

   ```bash
   docker stop jenkins
   ```

2. **Create a backup of Jenkins data**:

   ```bash
   docker cp jenkins:/var/jenkins_home /path/to/backup/folder
   ```

3. **Restart the Jenkins container**:

   ```bash
   docker start jenkins
   ```

---

## Conclusion

By following this guide, you have successfully installed Jenkins on your **macOS** using Docker, set up a basic **CI/CD pipeline** with SQL and release approval processes. You can now automate your builds, tests, and deployments, and track the approval steps