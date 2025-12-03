pipeline {
    agent any
    
    environment {
        // Ces variables restent utiles pour les logs et l'env du conteneur
        DOCKER_IMAGE   = "jenkins-demo-app"
        DOCKER_TAG     = "${BUILD_NUMBER}"
    }

    tools {
        nodejs "NodeJS"
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

        stage('SonarQube Analysis') {
            def scannerHome = tool 'SonarScanner';
            withSonarQubeEnv() {
                sh "${scannerHome}/bin/sonar-scanner"
            }
        }
        
        stage('Build & Deploy with Docker Compose') {
            steps {
                script {
                    // Optionnel : arrêter les conteneurs existants
                    sh """
                        echo "Stopping existing stack (if any)..."
                        docker compose down || true
                    """

                    // Build + run basé sur ton docker-compose.yml
                    // BUILD_NUMBER est passé à compose pour l'env du service
                    sh """
                        echo "Building and starting stack with docker compose..."
                        BUILD_NUMBER=${BUILD_NUMBER} docker compose up -d --build
                    """
                }
            }
        }
    }
    
    post {
        success {
            echo "Build #${BUILD_NUMBER} OK – stack démarrée via docker compose (port 3000)."
        }
        failure {
            echo "Build #${BUILD_NUMBER} en échec."
        }
        always {
            echo "Pipeline terminé pour le build #${BUILD_NUMBER}."
        }
    }
}
