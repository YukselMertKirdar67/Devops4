pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        IMAGE_NAME = 'yukselmert/devops4'
    }

    stages {
        stage('Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/YukselMertKirdar67/Devops4.git'
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
                sh 'kubectl apply -f k8s/deployment.yaml'
                sh 'kubectl apply -f k8s/service.yaml'
            }
        }
    }
}