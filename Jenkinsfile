pipeline {
    agent any

    environment {
        DOCKER_HUB_USER = 'narattscoward'
        IMAGE_NAME = 'todo-app'
        DOCKER_HUB_CREDS = 'docker-hub-credentials'
    }

    stages {

        stage('Build') {
            steps {
                echo 'Installing dependencies...'
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'npm test || true'
            }
        }

        stage('Containerize') {
            steps {
                echo 'Building Docker image...'
                sh "docker build -t ${DOCKER_HUB_USER}/${IMAGE_NAME}:latest ."
            }
        }

        stage('Push') {
            steps {
                echo 'Logging into Docker Hub...'
                withCredentials([usernamePassword(
                    credentialsId: "${DOCKER_HUB_CREDS}",
                    passwordVariable: 'DOCKER_PASS',
                    usernameVariable: 'DOCKER_USER'
                )]) {

                    sh "echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin"
                    sh "docker push ${DOCKER_HUB_USER}/${IMAGE_NAME}:latest"
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning workspace'
            sh "docker rmi ${DOCKER_HUB_USER}/${IMAGE_NAME}:latest || true"
        }
    }
}