pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "prathvipp/mywebsite2"
        K8S_CREDENTIAL = 'my-k8s-cluster'
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
                    sh "docker build -t ${DOCKER_IMAGE} ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    echo "Pushing Docker image ${DOCKER_IMAGE} to Docker Hub..."
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh "echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin"
                        sh "docker push ${DOCKER_IMAGE}"
                        sh "docker logout"
                    }
                }
            }
        }
        
        stage('Kubernetes Authentication Test') {
            steps {
                script {
                    sh 'kubectl cluster-info'  // Check if kubectl can connect to the cluster
                }
            }
        }
        
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    withKubeConfig([credentialsId: env.K8S_CREDENTIAL]) {
                        echo "Deploying to Kubernetes..."
                        sh "kubectl apply -f deployment.yaml"  // Direct path to the root level file
                        sh "kubectl get pods"
                    }
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
