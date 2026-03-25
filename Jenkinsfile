pipeline {
    agent any

    stages {

        stage('Checkout from GitHub') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Hussainbee57/k8n-app.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t k8n-app:${BUILD_NUMBER} .
                docker tag k8n-app:${BUILD_NUMBER} Hussainbee57/k8n-app:${BUILD_NUMBER}
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker push Hussainbee57/k8n-app:${BUILD_NUMBER}'
            }
        }
}

        
}
