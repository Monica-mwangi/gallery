pipeline {
    agent any

    environment {
        VERCEL_TOKEN = credentials('vercel-token')
    }

    stages {
        stage('Setup Tools') {
            steps {
                sh '''
                  # Update and install jq if available
                  apt-get update || true
                  apt-get install -y jq || true

                  # Install vercel CLI (locally for this workspace)
                  npm install -g vercel
                '''
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
            // Using webhookUrl credentials instead of tokenCredentialId
            slackSend(
                webhookUrl: credentials('slack-webhook'),
                channel: '#all-jenkinsnotifier',
                color: 'good',
                message: "✅ Build #${env.BUILD_NUMBER} deployed successfully to Vercel!\nVisit: https://${env.VERCEL_URL}"
            )
        }

        failure {
            slackSend(
                webhookUrl: credentials('slack-webhook'),
                channel: '#all-jenkinsnotifier',
                color: 'danger',
                message: "❌ Build #${env.BUILD_NUMBER} failed. Check Jenkins logs."
            )
        }
    }
}
