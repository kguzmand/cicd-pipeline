pipeline {
    agent any

    environment {
        APP_PORT = '3000'  // Puerto para main
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Set Logo') {
            steps {
                script {
                    sh 'cp ./src/logos/main-logo.svg ./src/logo.svg'
                }
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
                sh 'npm run build'
            }
        }

        stage('Docker Build & Run') {
            steps {
                script {
                    def imageName = 'nodemain:v1.0'
                    def containerId = sh(script: "docker ps -q --filter 'ancestor=${imageName}'", returnStdout: true).trim()
                    def usedPort = sh(script: "docker ps --filter 'publish=3000' --format '{{.ID}}'", returnStdout: true).trim()

                    if (usedPort) {
                         echo "Stopping and removing existing container using port 3000..."
                         sh "docker stop ${usedPort} || true"
                         sh "docker rm ${usedPort} || true"
                    }

                    echo "Building Docker image..."
                    sh "docker build -t ${imageName} ."

                    echo "Running container..."
                    sh "docker run -d -p 3000:3000 ${imageName}"
                }
            }
        }

        stage('Clean up old containers') {
            steps {
                script {
                    def imageName = 'nodemain:v1.0'
                    sh """
                    # Elimina solo los contenedores que usan la imagen actual
                    docker ps -a --filter ancestor=${imageName} -q | xargs -r docker rm -f
                    """
                }
            }
        }

    }
}
