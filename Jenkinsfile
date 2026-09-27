pipeline {
    agent any

    environment {
        DOCKER_USER = "adityashindee0733"
        BACKEND_IMAGE = "adityashindee0733/bookstore-backend"
        FRONTEND_IMAGE = "adityashindee0733/bookstore-frontend"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Backend Docker Image') {
            steps {
                bat 'docker build -t %BACKEND_IMAGE%:latest ./server'
            }
        }

        stage('Build Frontend Docker Image') {
            steps {
                bat 'docker build -t %FRONTEND_IMAGE%:latest ./client'
            }
        }

        stage('Push Docker Images') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    bat 'docker login -u %DOCKER_USERNAME% -p %DOCKER_PASSWORD%'
                    bat 'docker push %BACKEND_IMAGE%:latest'
                    bat 'docker push %FRONTEND_IMAGE%:latest'
                    bat 'docker logout'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat 'kubectl apply -f k8s/mongo.yaml'
                bat 'kubectl apply -f k8s/backend.yaml'
                bat 'kubectl apply -f k8s/frontend.yaml'

                bat 'kubectl rollout restart deployment/bookstore-backend'
                bat 'kubectl rollout restart deployment/bookstore-frontend'

                bat 'kubectl rollout status deployment/bookstore-backend'
                bat 'kubectl rollout status deployment/bookstore-frontend'
            }
        }
    }

    post {
        success {
            echo 'CI/CD Pipeline completed successfully!'
        }

        failure {
            echo 'CI/CD Pipeline failed. Check the console output.'
        }
    }
}