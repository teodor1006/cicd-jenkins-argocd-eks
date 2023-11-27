pipeline {
    agent any

    environment {
        registryCredential = 'ecr:us-east-1:awscreds'
        appRegistry = '865893227318.dkr.ecr.us-east-1.amazonaws.com/flask_image'
        monitorRegistry = "https://865893227318.dkr.ecr.us-east-1.amazonaws.com"
        GIT_REPO_NAME = "cicd-jenkins-argocd-eks"
        GIT_USER_NAME = "teodor1006"
    }

    stages {
        stage('Checkout and Install Dependencies') {
            steps {
                checkout scm
                script {
                    sh 'pip3 install -r requirements.txt'
                }
            }
        }
        
        stage('Build App Image') {
            steps {
                script {
                    dockerImage = docker.build("${appRegistry}:${BUILD_NUMBER}", "./")
                }
            }
        }

        stage('Upload App Image') {
            steps {
                script {
                    docker.withRegistry(monitorRegistry, registryCredential) {
                        dockerImage.push("${BUILD_NUMBER}")
                        dockerImage.push('latest')
                    }
                }
            }
        }

        stage('Updating the Deployment File') {
            steps {
                withCredentials([string(credentialsId: 'github', variable: 'GITHUB_TOKEN')]) {
                    script {
                        gitCredentials = "https://${GIT_USER_NAME}:${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME}.git"
                        sh """
                            git clone ${gitCredentials}
                            sed -i 's/replaceImageTag/${BUILD_NUMBER}/g' application.yaml
                            git add application.yaml
                            git commit -m "updated the image \${BUILD_NUMBER}"
                            git push origin main
                        """
                    }
                }
            } 
        }
    }
}











