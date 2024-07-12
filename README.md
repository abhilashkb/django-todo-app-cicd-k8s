# Django Todo App

<!---**Tutorial**: [Deploying Django Apps with Docker: A Step-by-Step Guide](https://betterstack.com/community/guides/scaling-python/dockerize-django/) -->

![Django todo app screenshot](screenshot.png)

## 🟢 Prerequisites

You must have [Python3](https://www.python.org/downloads/) and [pip](https://pypi.org/project/pip/) installed on your machine. You also need to set up [PostgreSQL](https://www.postgresql.org/download/). This project was tested against the following versions:

- Python 3.10.6
- Pip 22.0.2
- PostgreSQL 14.8

## 📦 Getting started


```markdown
# Django Postgres Kubernetes Deployment with Jenkins Pipeline

This repository contains a setup for automating the deployment of a Django application with a PostgreSQL database to a Kubernetes cluster using a Jenkins pipeline.

## Table of Contents

- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Setup Instructions](#setup-instructions)
  - [1. Clone the Repository](#1-clone-the-repository)
  - [2. Jenkins Pipeline Setup](#2-jenkins-pipeline-setup)
  - [3. Kubernetes Configuration](#3-kubernetes-configuration)
  - [4. Environment Variables](#4-environment-variables)
- [Running the Pipeline](#running-the-pipeline)
- [Contributing](#contributing)
- [License](#license)

## Project Structure

```plaintext
.
├── Jenkinsfile
├── django-app
│   ├── Dockerfile
│   ├── requirements.txt
│   └── ... (Django application files)
├── kubernetes
│   ├── django-deployment.yaml
│   ├── django-service.yaml
│   ├── postgress-pv.yaml
│   ├── postgres-deployment.yaml
│   └── postgres-service.yaml
└── README.md
```

## Prerequisites

- Docker
- Kubernetes cluster (Minikube or any other Kubernetes service)
- Jenkins server with necessary plugins installed
- Git

## Setup Instructions

### 1. Clone the Repository

```bash
git clone https://github.com/yourusername/your-repo-name.git
cd your-repo-name
```

### 2. Jenkins Pipeline Setup

Ensure your Jenkins server is running and configured with the necessary plugins:

1. **Docker Pipeline Plugin**
2. **Kubernetes Plugin**
3. **Git Plugin**

Create a new pipeline project in Jenkins and point it to this repository. The `Jenkinsfile` in the root of this repository contains the pipeline script.

Here's the provided Jenkins pipeline script:

```groovy
def getVersion() {
    commitid = sh returnStdout: true, script: '''git rev-parse HEAD'''
    return commitid
}

pipeline {
    agent any
    stages {
        stage('SCM checkout') {
            steps {
                git branch: 'main', credentialsId: '3556331b-958c-41cd-b636-918833670bc2', url: 'https://github.com/abhilashkb/django-todo-app-cicd' 

                script {
                    env.DOCK_TAG = getVersion().trim()
                }
            }
        }
        stage("Docker Build") {
            steps {
                sh "docker build . -t 224574/django-todo:${DOCK_TAG}"
            }
        }
        stage("Docker Push") {
            steps {
                echo "${DOCK_TAG}"
                withCredentials([string(credentialsId: 'dockerhubpwd', variable: 'dockerhubpwd')]) {
                    sh "docker login -u 224574 -p ${dockerhubpwd}"
                }
                sh "docker push 224574/django-todo:${DOCK_TAG}"
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh 'sed -i "s|tagname|$DOCK_TAG|" kubernetes/django-deployment.yaml'
                    withCredentials([file(credentialsId: 'my-k8s-config', variable: 'SECRET_FILE')]) {
                        withEnv(["KUBECONFIG=${SECRET_FILE}"]) {
                            sh 'kubectl get pods'
                            sh 'kubectl apply -f kubernetes/postgress-pv.yaml'
                            sh 'kubectl apply -f kubernetes/postgres-deployment.yaml'
                            sh 'kubectl apply -f kubernetes/postgres-service.yaml'
                            sh 'kubectl apply -f kubernetes/django-deployment.yaml'
                            sh 'kubectl apply -f kubernetes/django-service.yaml'
                        }
                    }
                }
            }
        }
    }
}
```

### 3. Kubernetes Configuration

Review the Kubernetes configuration files located in the `kubernetes` directory. Ensure the `django-deployment.yaml` and `postgres-deployment.yaml` files are correctly configured for your environment.

### 4. Environment Variables

Ensure the necessary environment variables are set in the Jenkins pipeline or Jenkins global configuration:

- `DOCK_TAG`: The tag for the Docker image, derived from the latest Git commit ID.
- `dockerhubpwd`: Docker Hub password for authentication.
- `SECRET_FILE`: Path to the Kubernetes config file for accessing the cluster.

## Running the Pipeline

Once the Jenkins pipeline is set up, you can trigger it manually or configure it to run automatically on code commits. The pipeline will perform the following steps:

1. **SCM Checkout**: Clone the latest code from the Git repository.
2. **Docker Build**: Create Docker images for the Django application.
3. **Docker Push**: Push the Docker images to Docker Hub.
4. **Deploy to Kubernetes**: Apply the Kubernetes configuration files to deploy the application and database.

Monitor the Jenkins console output for any errors and ensure the deployment is successful.

## Contributing

We welcome contributions to improve this project. Please fork the repository and create a pull request with your changes. Ensure that your code follows the existing code style and includes tests where necessary.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.
```

Feel free to adjust any part of this `README.md` to better match your specific project requirements and setup.