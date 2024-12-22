pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials'
        DOCKER_IMAGE = 'your-syedssaad/myapp:latest'
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout code from the repository
                git 'https://github.com/your-username/your-repo.git'
            }
        }

        stage('Build') {
            steps {
                // Build the application using Maven
                bat 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                // Run automated tests
                bat 'mvn test'
            }
        }

        stage('Build Docker Image') {
            steps {
                // Build Docker image
                bat 'docker build -t %DOCKER_IMAGE% .'
            }
        }

        stage('Push Docker Image') {
            steps {
                // Push Docker image to Docker Hub
                withCredentials([usernamePassword(credentialsId: env.DOCKER_CREDENTIALS_ID, usernameVariable: 'syedssaad', passwordVariable: 'Saad@123')]) {
                    bat 'echo %DOCKER_PASSWORD% | docker login -u %DOCKER_USERNAME% --password-stdin'
                    bat 'docker push %DOCKER_IMAGE%'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                // Deploy the application to Kubernetes
                bat 'kubectl apply -f k8s/deployment.yaml'
                bat 'kubectl apply -f k8s/service.yaml'
            }
        }
    }

    post {
        always {
            // Clean up workspace
            cleanWs()
        }
    }
}
