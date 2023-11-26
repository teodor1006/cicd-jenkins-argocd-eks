pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        IMAGE_REPO_NAME = 'flask_image'
        IMAGE_TAG = 'latest'
        ECR_REGISTRY = "865893227318.dkr.ecr.${AWS_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
    }

    stages {
        stage('Checkout and Install Dependencies') {
            steps {
                script {
                    checkout scm
                    sh 'pip3 install -r requirements.txt'
                }
            }
        }

        stage('Build and Push Image to ECR') {
            steps {
                script {
                    // Log in to ECR
                    withCredentials([aws(credentialsId: 'awscreds', region: AWS_REGION)]) {
                        sh "aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${ECR_REGISTRY}"
                    }

                    // Build Docker image
                    def dockerImage = docker.build("${IMAGE_REPO_NAME}:${IMAGE_TAG}")

                    // Tag and push image to ECR
                    dockerImage.tag("${ECR_REGISTRY}:${IMAGE_TAG}")
                    docker.withRegistry(ECR_REGISTRY, 'ecr:latest') {
                        dockerImage.push("${IMAGE_TAG}")
                    }
                }
            }
        }
    }
}






