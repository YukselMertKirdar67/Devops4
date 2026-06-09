# 📚 Library Management System – Spring Boot
 
## 📌 Project Overview
 
This project is a **Spring Boot web application** developed for **SWE304 Project Study 4 (2026)**.
 
It implements a **CI/CD pipeline using Jenkins and Kubernetes (Minikube)**, where every push to the GitHub repository automatically triggers the pipeline — building, containerizing, pushing to DockerHub, and deploying to a local Kubernetes cluster.
 
The application is a **Library Management System** where users can manage books, members, and borrowing operations. It features both a web interface and REST API endpoints.
 
---
 
## 🛠️ Technology Stack
 
| Layer | Technology |
|---|---|
| Language | Java |
| Framework | Spring Boot |
| Build Tool | Gradle |
| Frontend | Thymeleaf |
| Containerization | Docker |
| Image Registry | DockerHub |
| CI/CD Server | Jenkins |
| Orchestration | Kubernetes (Minikube) |
| Source Control | GitHub |
 
---
 
## 🚀 Features
 
### 📖 Book Management
- Create, Read, Update, Delete books
### 👤 Member Management
- Create, Read, Update, Delete members
### 🔄 Borrow System
- Members can borrow and return books
### 🌐 Web Interface
- Server-side rendered pages using Thymeleaf
---
 
## ⚙️ CI/CD Pipeline (Jenkins)
 
Triggered automatically on every GitHub push or merge:
 
| Stage | Action |
|---|---|
| Clone | Clones the project from GitHub to the local machine |
| Build | Builds the project and generates a JAR file (`./gradlew bootJar`) |
| Docker Image | Builds a Docker image |
| Docker Login | Logs in to DockerHub using stored credentials |
| Docker Push | Pushes the image to DockerHub |
| K8s Deploy | Applies `deploy.yaml` and `service.yaml`, then restarts the deployment |
 
---
 
## 🏗️ Architecture
 
```
Developer → GitHub (push/merge)
               ↓ webhook trigger
           Jenkins Server (local)
               ↓
    ┌────────────────────────────────────┐
    │  Clone        → git pull           │
    │  Build        → gradlew bootJar    │
    │  Docker Image → docker build       │
    │  Docker Login → dockerhub login    │
    │  Docker Push  → ────────────────→ DockerHub
    │  K8s Deploy   → kubectl apply      │
    └────────────────────────────────────┘
               ↓
     Kubernetes (Minikube) ←── pulls image from DockerHub
               ↓
       Pod 1 | Pod 2 (scaled)
```
 
---
 
## 📁 Project Structure
 
```
├── src/
│   └── main/
│       └── java/
│           └── com/example/library/
│               └── LibraryApplication.java
├── docs/
│   ├── jenkins-pipeline.png
│   ├── jenkins-trigger.png
│   ├── jenkins-console.png
│   ├── k8s-deploy-console.png
│   ├── k8s-pods.png
│   ├── k8s-service.png
│   ├── app-running.png
│   └── k8s-scaled.png
├── deploy.yaml
├── service.yaml
├── Dockerfile
├── Jenkinsfile
├── build.gradle
└── README.md
```
 
---
 
## 🔧 Jenkinsfile
 
```groovy
pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        IMAGE_NAME = 'your-dockerhub-username/your-app'
    }
    stages {
        stage('Clone') {
            steps {
                git branch: 'master', url: 'https://github.com/your-username/your-repo.git'
            }
        }
        stage('Build') {
            steps {
                sh './gradlew bootJar'
            }
        }
        stage('Docker Image') {
            steps {
                sh 'docker build -t ${IMAGE_NAME}:latest .'
            }
        }
        stage('Docker Login') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
        stage('Docker Push') {
            steps {
                sh 'docker push ${IMAGE_NAME}:latest'
            }
        }
        stage('K8s Deploy') {
            steps {
                sh 'kubectl apply -f deploy.yaml'
                sh 'kubectl apply -f service.yaml'
                sh 'kubectl rollout restart deployment/your-app-deployment'
            }
        }
    }
}
```
 
---
 
## ☸️ Kubernetes Manifests
 
### deploy.yaml
 
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: your-app-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: your-app
  template:
    metadata:
      labels:
        app: your-app
    spec:
      containers:
        - name: your-app
          image: your-dockerhub-username/your-app:latest
          imagePullPolicy: Always
          ports:
            - containerPort: 8081
```
 
### service.yaml
 
```yaml
apiVersion: v1
kind: Service
metadata:
  name: your-app-service
spec:
  type: NodePort
  selector:
    app: your-app
  ports:
    - port: 8081
      targetPort: 8081
      nodePort: 30081
```
 
---
 
## ☸️ Kubernetes (Minikube) Commands
 
```bash
# Minikube'u başlat
minikube start
 
# Manifest'leri uygula
kubectl apply -f deploy.yaml
kubectl apply -f service.yaml
 
# Pod durumunu kontrol et
kubectl get pods
 
# Servisleri kontrol et
kubectl get services
 
# Uygulamaya eriş
minikube service your-app-service
 
# 2 pod'a scale et
kubectl scale deployment your-app-deployment --replicas=2
kubectl get pods
```
 
---
 
## 🧪 How to Run Locally
 
1. Minikube'u başlat:
```bash
minikube start
```
 
2. Projeyi build et:
```bash
./gradlew bootJar
```
 
3. Docker ile çalıştır:
```bash
docker build -t your-dockerhub-username/your-app:latest .
docker run -p 8081:8081 your-dockerhub-username/your-app:latest
```
 
Uygulama adresi: `http://localhost:8081`
 
---
 
## 📷 Deployment Proof
 
### ✅ Jenkins Pipeline – All Stages Passing
![Jenkins Pipeline](docs/jenkins-pipeline.png)
 
---
 
### 🔁 Jenkins – Triggered by GitHub Push
![Jenkins Trigger](docs/jenkins-trigger.png)
 
---
 
### 📋 Jenkins Console – Docker Push
![Jenkins Console](docs/jenkins-console.png)
 
---
 
### 📋 Jenkins Console – K8s Deploy
![K8s Deploy Console](docs/k8s-deploy-console.png)
 
---
 
### ☸️ Kubernetes Pods Running
![K8s Pods](docs/k8s-pods.png)
 
---
 
### 🔌 Kubernetes Service
![K8s Service](docs/k8s-service.png)
 
---
 
### 🌍 Application Running on Kubernetes
![App Running](docs/app-running.png)
 
---
 
### 📈 Application Scaled to 2 Pods
![K8s Scaled](docs/k8s-scaled.png)
 
---
 
## 🧠 Learning Outcomes
 
- Jenkins CI/CD pipeline kurulumu ve yapılandırması
- GitHub webhook entegrasyonu
- Docker image build ve DockerHub'a push otomasyonu
- Kubernetes Deployment ve Service manifest yazımı
- Minikube yerel cluster yönetimi
- `kubectl scale` ile horizontal pod scaling
- Spring Boot web uygulaması geliştirme
- Thymeleaf ile server-side rendering
- Gradle build lifecycle yönetimi
