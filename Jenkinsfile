pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials'
        DOCKER_IMAGE = 'your-dockerhub-username/your-image-name'
        DOCKER_TAG = 'latest'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
                }
            }
        }

        stage('Test Docker Image') {
            steps {
                script {
                    def customImage = docker.image("${DOCKER_IMAGE}:${DOCKER_TAG}")
                    customImage.inside {
                        sh 'echo "Running test commands inside container"'
                        // Example: Run a command to check if the container launches correctly
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                        def customImage = docker.image("${DOCKER_IMAGE}:${DOCKER_TAG}")
                        customImage.push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    def kubectlCmd = 'kubectl apply -f deployment.yaml'
                    sh kubectlCmd
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}

