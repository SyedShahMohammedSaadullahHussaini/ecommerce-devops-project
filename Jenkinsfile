pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials'  // ID of your Jenkins Docker credentials
        DOCKER_IMAGE = 'syedssaad/myapp:latest'          // Replace with your Docker Hub username and image name
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout code from the repository
                git url: 'https://github.com/SyedShahMohammedSaadullahHussaini/ecommerce-devops-project.git', branch: 'main'
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
                bat "docker build -t %DOCKER_IMAGE% ."
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        bat """
                        docker login -u %DOCKER_USER% -p %DOCKER_PASS%
                        docker push %DOCKER_IMAGE%
                        """
                    }
                }
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
