pipeline {
    agent any

    tools {
        nodejs 'node-16.20.2'
    }

    environment {
        IMAGE_NAME    = "${env.BRANCH_NAME == 'main' ? 'nodemain' : 'nodedev'}"
        IMAGE_TAG     = "v1.0"
        PORT          = "${env.BRANCH_NAME == 'main' ? '3000' : '3001'}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo "Installing dependencies..."
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                echo "Running unit tests..."
                sh 'CI=true npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image ${IMAGE_NAME}:${IMAGE_TAG}..."
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying ${IMAGE_NAME} to port ${PORT}..."
                sh """
                    # Stopping existing conatiner
                    docker rm -f ${IMAGE_NAME} || true
                    # Starting new container
                    docker run -d --name ${IMAGE_NAME} -p ${PORT}:3000 ${IMAGE_NAME}:${IMAGE_TAG}
                """
                echo "Application successfully running on http://localhost:${PORT}"
            }
        }
    }
}
