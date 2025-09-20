pipeline {
    agent any

    environment {
        VERCEL_TOKEN = credentials('vercel-token')  // Your Vercel token
        SLACK_WEBHOOK = credentials('slack-webhook') // Slack webhook URL
    }

    triggers {
        pollSCM('* * * * *')  // Poll repository every minute
    }

    stages {
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
                sh 'npm test'
            }
            post {
                failure {
                    mail to: 'mwangimonica123@gmail.com',
                         subject: "Jenkins Build #${env.BUILD_NUMBER} Failed",
                         body: "Tests failed. Check Jenkins console output."
                }
            }
        }

        stage('Deploy to Vercel') {
            steps {
                // Deploy and capture the URL
                script {
                    env.VERCEL_URL = sh(
                        script: "npx vercel --prod --token $VERCEL_TOKEN --confirm --json | jq -r '.url'",
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
                message: "✅ Build #${env.BUILD_NUMBER} deployed successfully to Vercel!\nVisit: https://${env.VERCEL_URL}"
            )
        }

        failure {
            slackSend(
                channel: '#all-jenkinsnotifier',
                color: 'danger',
                message: "❌ Build #${env.BUILD_NUMBER} failed. Check Jenkins logs."
            )
        }
    }
}
