pipeline {
    agent any

    tools {
        nodejs 'NodeJS-18' // Make sure you have NodeJS tool configured in Jenkins
    }

    stages {
        stage('Build Angular App') {
            steps {
                echo 'Building Angular application...'
                sh '''
                    npm install
                    npm run build -- --configuration production
                '''
            }
        }

        stage('Archive Build Artifacts') {
            steps {
                echo 'Creating build archive...'
                sh '''
                    cd dist
                    tar -czf ../angular-app-build.tar.gz *
                '''
                archiveArtifacts artifacts: 'angular-app-build.tar.gz', fingerprint: true
                echo 'Build artifacts archived successfully!'
            }
        }
    }

    post {
        success {
            echo "✅ Build successful!"
        }
        failure {
            echo "❌ Build failed!"
        }
    }
}
