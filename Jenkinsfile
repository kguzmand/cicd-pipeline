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

        stage('Docker Push') {
            steps {
                script {
                    def dockerUser = "kguzmand"
                    def imageName = "${dockerUser}/nodemain:v1.0" // Para main
            
                    echo "Logging into Docker Hub..."
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                    }

                    echo "Tagging image..."
                    sh "docker tag nodemain:v1.0 ${imageName}" // Para main

                    echo "Pushing image to Docker Hub..."
                    sh "docker push ${imageName}"
                }
            }
       }

    }
}
