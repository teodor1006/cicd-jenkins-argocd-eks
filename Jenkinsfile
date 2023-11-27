pipeline {
    agent any

    environment {
        registryCredential = 'ecr:us-east-1:awscreds'
        appRegistry = '865893227318.dkr.ecr.us-east-1.amazonaws.com/flask_image'
        monitorRegistry = "https://865893227318.dkr.ecr.us-east-1.amazonaws.com"
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
    }
}











