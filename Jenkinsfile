pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE   = "jenkins-demo-app"
        DOCKER_TAG     = "${BUILD_NUMBER}"
        FULL_IMAGE     = "${DOCKER_IMAGE}:${DOCKER_TAG}"
        // Si ton docker-compose référence juste "jenkins-demo-app:latest",
        // tu peux aussi choisir de ne PAS tagger par BUILD_NUMBER
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }
        
        stage('Run Tests') {
            steps {
                sh 'npm test'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                // 2 options :
                // 1) Build direct avec docker (si compose utilise cette image)
                sh "docker build -t ${FULL_IMAGE} ."
                // 2) Ou laisser compose faire le build:
                // sh "docker compose build"
            }
        }
        
        stage('Deploy with Docker Compose') {
            steps {
                script {
                    // On se base sur le docker-compose du repo
                    // (par défaut docker-compose.yml à la racine)
                    sh """
                        echo "Stopping existing stack..."
                        docker compose down || true

                        echo "Starting stack with docker compose..."
                        docker compose up -d
                    """
                }
            }
        }
    }
    
    post {
        success {
            echo "Build #${BUILD_NUMBER} OK – déploiement effectué via docker compose."
        }
        failure {
            echo "Build #${BUILD_NUMBER} en échec."
        }
        always {
            echo "Pipeline terminé pour le build #${BUILD_NUMBER}."
        }
    }
}
