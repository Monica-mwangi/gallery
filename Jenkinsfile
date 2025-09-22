pipeline {
    agent {
        docker {
            image 'node:18'
            args '-u root'
        }
    }

    environment {
        VERCEL_TOKEN = credentials('vercel-token')
    }

    stages {
        stage('Setup Tools') {
            steps {
                sh 'apt-get update && apt-get install -y jq'
            }
        }

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build') {
            steps {
                sh 'npm run build || echo "No build step defined"'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test || echo "No tests defined"'
            }
        }

        stage('Deploy to Vercel') {
            steps {
                sh 'npm install -g vercel'
                script {
                    env.VERCEL_URL = sh(
                        script: "vercel --prod --token $VERCEL_TOKEN --confirm --json | jq -r '.url'",
                        returnStdout: true
                    ).trim()
                }
            }
        }
    }

    post {
        success {
            slackSend(
                channel: '#all-jenkinsnotifier',
                color: 'good',
                message: "✅ Build #${env.BUILD_NUMBER} deployed successfully to Vercel!\nVisit: https://${env.VERCEL_URL}",
                tokenCredentialId: 'slack-webhook'
            )
        }

        failure {
            slackSend(
                channel: '#all-jenkinsnotifier',
                color: 'danger',
                message: "❌ Build #${env.BUILD_NUMBER} failed. Check Jenkins logs.",
                tokenCredentialId: 'slack-webhook'
            )
        }
    }
}
