pipeline {
    agent any

    environment {
        REGISTRY = "docker.io/your-dockerhub-username"
        FRONTEND_IMAGE = "three-tier-notes-frontend"
        BACKEND_IMAGE  = "three-tier-notes-backend"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/your-username/three-tier-notes-app.git'
            }
        }

        stage('Build Docker Images') {
            steps {
                sh 'docker build -t $REGISTRY/$BACKEND_IMAGE:latest ./backend'
                sh 'docker build -t $REGISTRY/$FRONTEND_IMAGE:latest ./frontend'
            }
        }

        stage('Push Docker Images') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker push $REGISTRY/$BACKEND_IMAGE:latest'
                    sh 'docker push $REGISTRY/$FRONTEND_IMAGE:latest'
                }
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker compose -f docker-compose.yml down'
                sh 'docker compose -f docker-compose.yml up -d --build'
            }
        }
    }
}
