node {

    env.DOCKER_HOST = "unix:///home/user/.docker/desktop/docker.sock"

    stage('Checkout') {
        git branch: 'master',
            url: 'https://github.com/Bhuvaneshwari-bhu/k8n.git'
    }

    stage('Install Dependencies') {
        sh 'npm install'
    }

    stage('Build Docker Image') {
        sh '''
        docker build -t k8n:${BUILD_NUMBER} .
        docker tag k8n:${BUILD_NUMBER} shaikhussainbee/k8n:${BUILD_NUMBER}
        '''
    }

    stage('Docker Login & Push') {
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

    stage('Deploy Container') {
        sh '''
        # Remove old container if exists
        docker rm -f k8n-container || true

        # Decide port
        if lsof -i:3000 >/dev/null 2>&1; then
            echo "Port 3000 busy → using 3005"
            PORT=3005
        else
            echo "Port 3000 free → using 3000"
            PORT=3000
        fi

        # Run container (app runs on 8080 inside)
        docker run -d -p $PORT:8080 --name k8n-container shaikhussainbee/k8n:${BUILD_NUMBER}

        echo "🚀 App running at: http://localhost:$PORT"
        '''
    }
}
