pipeline {
agent any

```
environment {
    DOCKER_HOST = "unix:///home/user/.docker/desktop/docker.sock"
}

stages {

    stage('Checkout from GitHub') {
        steps {
            git branch: 'master',
                url: 'https://github.com/Bhuvaneshwari-bhu/k8n.git'
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
            docker build -t k8n:${BUILD_NUMBER} .
            docker tag k8n:${BUILD_NUMBER} shaikhussainbee/k8n:${BUILD_NUMBER}
            '''
        }
    }

    stage('Docker Login & Push') {
        steps {
            withCredentials([usernamePassword(
                credentialsId: 'docker-hub-cred',
                usernameVariable: 'DOCKER_USER',
                passwordVariable: 'DOCKER_PASS'
            )]) {
                sh '''
                echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                docker push shaikhussainbee/k8n:${BUILD_NUMBER}
                '''
            }
        }
    }

    stage('Deploy Container') {
        steps {
            sh '''
            docker stop k8n-container || true
            docker rm k8n-container || true

            docker run -d -p 3000:8080 --name k8n-container shaikhussainbee/k8n:${BUILD_NUMBER}
            '''
        }
    }
}

post {
    success {
        echo '✅ Pipeline executed successfully!'
    }
    failure {
        echo '❌ Pipeline failed. Check logs!'
    }
}
```

}
