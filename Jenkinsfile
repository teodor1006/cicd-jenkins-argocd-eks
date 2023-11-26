pipeline {
    agent any {
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
                        sh "aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
                    }
                }
            }

            stage('Cloning git') {
                steps {
                    script {
                        checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: '']])
                    }
                }
            }
        }
    }
}
