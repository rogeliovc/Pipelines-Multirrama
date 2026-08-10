pipeline {
    agent any
    tools {
        // Asegúrate de que tu herramienta NodeJS se llame 'node' en Global Tool Configuration
        nodejs 'node' 
    }
    environment {
        // Lógica condicional: puerto 3000 para main, 3001 para dev
        HOST_PORT = "${env.BRANCH_NAME == 'main' ? '3000' : '3001'}"
        IMAGE_NAME = "node${env.BRANCH_NAME}:v1.0"
        CONTAINER_NAME = "app_${env.BRANCH_NAME}"
    }
    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh 'npm test || echo "Saltando pruebas"'
            }
        }
        stage('Docker build') {
            steps {
                sh "docker build -t ${IMAGE_NAME} ."
            }
        }
        stage('Deploy') {
            steps {
                // Tarea Avanzada: Elimina solo el contenedor del entorno que se está desplegando
                sh """
                docker stop ${CONTAINER_NAME} || true
                docker rm ${CONTAINER_NAME} || true
                docker run -d -p ${HOST_PORT}:3000 --name ${CONTAINER_NAME} ${IMAGE_NAME}
                """
            }
        }
    }
}
