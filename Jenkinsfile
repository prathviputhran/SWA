pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "prathvipp/mywebsite4"
        AWS_REGION = 'us-east-1'  // Example region
        EKS_CLUSTER_NAME = 'my-k8s-cluster'  // Replace with your EKS cluster name
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
        
        stage('Authenticate with AWS and Update Kubeconfig') {
            steps {
                script {
                    // Authenticate with AWS and set up kubeconfig for kubectl (assuming AWS credentials are set up)
                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-credentials']]) {
                        sh "aws eks --region ${AWS_REGION} update-kubeconfig --name ${EKS_CLUSTER_NAME}"
                    }
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                script {
                    // Deploy Docker image to EKS cluster using kubectl
                    sh "kubectl apply -f deployment.yaml"  // Assuming deployment.yaml is configured
                    sh "kubectl get pods"
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
