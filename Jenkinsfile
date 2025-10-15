pipeline {
  agent any

  environment {
    DOCKER_IMAGE = "niishantdev/angular-frontend"
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Prepare Node') {
      steps {
        script {
          def nodeHome = tool name: 'NodeJS_20', type: 'jenkins.plugins.nodejs.tools.NodeJSInstallation'
          env.PATH = "${nodeHome}/bin:${env.PATH}"
        }
      }
    }

    stage('Install') {
      steps { sh 'npm ci' }
    }

    stage('Build') {
      steps { sh 'npm run build -- --configuration production' }
    }

    stage('Docker Build & Push') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
          sh 'docker build -t $DOCKER_USER/angular-frontend:latest .'
          sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
          sh 'docker push $DOCKER_USER/angular-frontend:latest'
        }
      }
    }

    stage('Deploy to Test') {
      steps {
        withCredentials([sshUserPrivateKey(credentialsId: 'test-server-ssh', keyFileVariable: 'SSH_KEY')]) {
          sh """
            ssh -o StrictHostKeyChecking=no -i ${SSH_KEY} admin@3.90.210.125 '
              docker pull yourdockerhubuser/angular-frontend:latest
              docker stop angular-frontend || true
              docker rm angular-frontend || true
              docker run -d -p 4200:80 --name angular-frontend yourdockerhubuser/angular-frontend:latest
            '
          """
        }
      }
    }
  }

  post {
    always { cleanWs() }
  }
}
