pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "prathvipp/mywebsite"
    }
    stages {
        stage('Clone Repository') {
            steps {
                    git branch: 'main', url: 'https://github.com/prathviputhran/SWA.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image ${DOCKER_IMAGE}..."
                    sh 'docker --version'  // Check Docker version
                    sh 'docker build -t ${DOCKER_IMAGE} .'
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    echo "Pushing Docker image ${DOCKER_IMAGE} to Docker Hub..."
                    sh 'docker push ${DOCKER_IMAGE}'
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    echo "Deploying to Kubernetes..."
                    // Assuming kubectl is already configured in Jenkins to interact with K8s cluster
                    sh 'kubectl version'  // Check kubectl version and ensure it's connected to the cluster
                    sh 'kubectl apply -f kubernetes/deployment.yaml'
                    sh 'kubectl apply -f kubernetes/service.yaml'
                }
            }
        }
    }
    post {
        success {
            echo 'Deployment to Kubernetes succeeded!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}
