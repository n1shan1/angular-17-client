pipeline {
    agent {
        docker {
            image 'docker:dind'
            args '--privileged -v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub')
        IMAGE_NAME = "${DOCKERHUB_CREDENTIALS_USR}/angular-frontend:latest"
    }

    stages {
        stage('Build Angular App') {
            steps {
                echo 'Installing Node.js and building Angular application...'
                sh '''
                    # Install Node.js in the Docker container
                    apk add --no-cache nodejs npm
                    npm install
                    npm run build -- --configuration production
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh '''
                    # Build the Docker image directly using docker build
                    docker build -t ${IMAGE_NAME} .
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                echo 'Pushing Docker image to Docker Hub...'
                sh '''
                    echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                    docker push ${IMAGE_NAME}
                '''
            }
        }

        stage('Cleanup') {
            steps {
                echo 'Cleaning up...'
                sh 'docker system prune -f'
            }
        }
    }

    post {
        success {
            echo "✅ Build and push successful!"
        }
        failure {
            echo "❌ Build failed!"
        }
    }
}
