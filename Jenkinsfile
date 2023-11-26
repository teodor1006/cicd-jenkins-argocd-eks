pipeline {
    agent any

    environment {
        AWS_ACCOUNT_ID = "865893227318"
        AWS_REGION = "us-east-1"
        IMAGE_REPO_NAME = "flask_image"
        IMAGE_TAG = "latest"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
    }

    stages {
        stage('Checkout and Install Dependencies') {
            steps {
                checkout scm
                script {
                    // Install dependencies (you may need to adjust this based on your project structure)
                    sh 'pip3 install -r requirements.txt'
                }
            }
        }

        stage('Build and Push Image to ECR') {
            steps {
                script {
                    // Retrieve AWS credentials from Jenkins credentials
                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'awscreds', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
                        
                        // Log in to AWS ECR using Docker CLI
                        sh """
                            aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
                        """
                        
                        // Build and push Docker image to ECR
                        dockerImage = docker.build("${REPOSITORY_URI}:${IMAGE_TAG}")
                        docker.withRegistry("${REPOSITORY_URI}", 'ecr:latest') {
                            dockerImage.push("${IMAGE_TAG}")
                        }
                    }
                }
            }
        }
    }
}









