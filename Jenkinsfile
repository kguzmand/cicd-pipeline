pipeline {
    agent any

    environment {
        APP_PORT = '3001'  // Puerto para dev
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
                    sh 'cp ./src/logos/dev-logo.svg ./src/logo.svg'
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
                    def imageName = 'nodedev:v1.0'
                    def containerId = sh(script: "docker ps -q --filter 'ancestor=${imageName}'", returnStdout: true).trim()
                    def usedPort = sh(script: "docker ps --filter 'publish=3001' --format '{{.ID}}'", returnStdout: true).trim()

                    if (usedPort) {
                        echo "Stopping and removing existing container using port 3001..."
                        sh "docker stop ${usedPort} || true"
                        sh "docker rm ${usedPort} || true"
                    }

                    echo "Building Docker image..."
                    sh "docker build -t ${imageName} ."

                    echo "Running container..."
                    sh "docker run -d -p 3001:3001 ${imageName}"
                }
            }
        }

        stage('Clean up old containers') {
            steps {
                script {
                    def imageName = 'nodedev:v1.0'
                    sh """
                    # Elimina solo los contenedores de la imagen 'nodedev:v1.0'
                    docker ps -a --filter ancestor=${imageName} -q | xargs -r docker rm -f
                    """
                }
            }
        }

    }
}
