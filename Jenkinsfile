pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials'  // ID of your Jenkins Docker credentials
        DOCKER_IMAGE = 'syedssaad/myapp:latest'          // Replace with your Docker Hub username and image name
        KUBE_CONFIG = 'C:Users\saad\.kube\config'             // Kubernetes config file (if needed)
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
            parallel {
                stage('Unit Tests') {
                    steps {
                        // Run unit tests
                        bat 'mvn test -Dtest=UnitTests'
                    }
                }
                stage('Integration Tests') {
                    steps {
                        // Run integration tests
                        bat 'mvn test -Dtest=IntegrationTests'
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                // Build Docker image
                bat "docker build -t %DOCKER_IMAGE% ."
            }
        }

        stage('Scan Docker Image') {
            steps {
                // Scan the Docker image for vulnerabilities using Trivy
                bat 'trivy image %DOCKER_IMAGE%'
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

        stage('Deploy') {
            steps {
                script {
                    // Deploy the application using kubectl or similar deployment tool
                    bat "kubectl --kubeconfig=%KUBE_CONFIG% apply -f deployment.yaml"
                }
            }
        }
    }

    post {
        always {
            // Clean up workspace
            cleanWs()
        }

        success {
            // Send success notification (optional, e.g., via Slack)
            slackSend(channel: '#deployments', message: "Build ${env.BUILD_TAG} succeeded!")
        }

        failure {
            // Send failure notification (optional, e.g., via Slack)
            slackSend(channel: '#deployments', message: "Build ${env.BUILD_TAG} failed!")
        }
    }
}
