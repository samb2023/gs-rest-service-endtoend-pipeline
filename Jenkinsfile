pipeline {
    agent any

    environment {
        // Final image path in GHCR
        IMAGE_NAME = "ghcr.io/samb2023/my-image"
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
                    // Uses your multi-stage Dockerfile in repo root
                    dockerImage = docker.build("${IMAGE_NAME}:${BUILD_NUMBER}")
                }
            }
        }

        stage('Push to GHCR') {
            steps {
                script {
                    // 'git-code' must be the Jenkins credential ID
                    docker.withRegistry('https://ghcr.io', 'git-code') {
                        dockerImage.push("${BUILD_NUMBER}")
                        dockerImage.push("latest")
                    }
                }
            }
        }
    }

    post {
        always {
            // There is no jar in workspace now (jar built *inside* image),
            // so archiving can be skipped or left out.
            cleanWs()
        }
    }
}
