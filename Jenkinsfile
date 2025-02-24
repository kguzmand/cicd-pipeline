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
                sh '''
                docker build -t nodedev:v1.0 .
                docker run -d -p 3001:3001 nodedev:v1.0
                '''
            }
        }
    }
}
