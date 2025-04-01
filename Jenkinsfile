pipeline {
    agent any
    environment {
        REGISTRY = 'your-registry'      // z.B. Docker Hub oder private Registry
        IMAGE_NAME = 'your-app'
    }
    stages {
        stage('Checkout') {
            steps {
                // Git-Repository auschecken
                git 'https://github.com/your_user/your_repo.git'
            }
        }
        stage('Install Dependencies') {
            steps {
                // npm Abhängigkeiten installieren
                sh 'npm install'
            }
        }
        stage('Security Audit') {
            steps {
                // Sicherheitscheck mit npm audit
                sh 'npm audit'
            }
        }
        stage('Build Docker Image') {
            steps {
                // Docker Image bauen
                sh "docker build -t ${REGISTRY}/${IMAGE_NAME}:latest ."
            }
        }
        stage('Push Docker Image') {
            steps {
                // Authentifizieren und Image in Registry pushen
                withCredentials([usernamePassword(credentialsId: 'docker-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh "docker login -u $DOCKER_USER -p $DOCKER_PASS ${REGISTRY}"
                    sh "docker push ${REGISTRY}/${IMAGE_NAME}:latest"
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                // Deployment aktualisieren (Voraussetzung: kubectl ist auf dem Agent konfiguriert)
                sh "kubectl set image deployment/your-deployment your-container=${REGISTRY}/${IMAGE_NAME}:latest"
            }
        }
    }
    post {
        failure {
            echo 'Pipeline fehlgeschlagen. Bitte überprüfen Sie die Logs.'
        }
    }
}
