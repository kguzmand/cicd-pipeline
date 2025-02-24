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
                sh '''
                docker build -t nodemain:v1.0 .
                docker run -d -p 3000:3000 nodemain:v1.0
                '''
            }
        }
    }
}
