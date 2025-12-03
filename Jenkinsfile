pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = "jenkins-demo-app"
        CONTAINER_NAME = "jenkins-demo-app"
        DOCKER_TAG = "${BUILD_NUMBER}"
        FULL_IMAGE = "${DOCKER_IMAGE}:${DOCKER_TAG}"
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
                script {
                    sh "docker build -t ${FULL_IMAGE} ."
                }
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    sh """
                        if [ \$(docker ps -aq -f name=${CONTAINER_NAME}) ]; then
                          echo "Stopping old container..."
                          docker stop ${CONTAINER_NAME} || true
                          echo "Removing old container..."
                          docker rm ${CONTAINER_NAME} || true
                        fi
                    """

                    sh """
                        echo "Starting new container..."
                        docker run -d --name ${CONTAINER_NAME} \\
                          -p 3000:3000 \\
                          ${FULL_IMAGE}
                    """
                }
            }
        }
    }
    
    post {
        success {
            echo "Build #${BUILD_NUMBER} OK – déploiement effectué."
        }
        failure {
            echo "Build #${BUILD_NUMBER} en échec."
        }
        always {
            echo "Pipeline terminé pour le build #${BUILD_NUMBER}."
        }
    }
}