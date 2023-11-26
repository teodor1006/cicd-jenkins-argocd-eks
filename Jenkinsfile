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
        stage('Logging into AWS ECR') {
          steps {
            script {
              withCredentials([string(credentialsId: 'awscreds', variable: 'AWS_ACCESS_KEY_ID'), string(credentialsId: 'awscreds', variable: 'AWS_SECRET_ACCESS_KEY')]) {
                  sh "aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
            }
        }
    }
}


        stage('Cloning git') {
            steps {
                script {
                    checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/teodor1006/cicd-jenkins-argocd-eks.git']])
                }
            }
        }

        stage('Running Unit Tests') {
            steps {
                script {
                    // Assuming your unit tests are in a file named unittests.py
                    sh 'python3 unittests.py'
                }
            }
        }

        stage('Building Image'){
            steps {
                script {
                    dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
                }
            }
        }

        stage('Pushing to ECR') {
            steps {
                script {
                    // Tag the Docker image with the ECR repository URI
                    dockerImage.tag("${REPOSITORY_URI}:${IMAGE_TAG}")

                    // Push the Docker image to ECR
                    docker.withRegistry("${REPOSITORY_URI}", 'ecr:latest') {
                        dockerImage.push("${IMAGE_TAG}")
                    }
                }
            }
        }
    }
}


