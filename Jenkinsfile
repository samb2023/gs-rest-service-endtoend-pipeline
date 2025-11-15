pipeline {
    agent any

    environment {
        IMAGE_NAME = "ghcr.io/samb2023/my-image"   // repo/name in GHCR
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn -f complete/pom.xml clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${IMAGE_NAME}:${BUILD_NUMBER}")
                }
            }
        }

        stage('Push to GHCR') {
            steps {
                script {
                    docker.withRegistry('https://ghcr.io', 'git-code') {
                        // Push versioned tag
                        dockerImage.push("${BUILD_NUMBER}")
                        // Push 'latest'
                        dockerImage.push("latest")
                    }
                }
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'complete/target/*.jar', fingerprint: true
            cleanWs()
        }
    }
}
