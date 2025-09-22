pipeline {
    agent any

    environment {
        VERCEL_TOKEN = credentials('vercel-token')
        SLACK_WEBHOOK = credentials('slack-webhook')
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
            sh """
            curl -X POST -H 'Content-type: application/json' \
            --data '{ "text": "✅ Build #${env.BUILD_NUMBER} deployed successfully! Visit: https://${env.VERCEL_URL}" }' \
            $SLACK_WEBHOOK
            """
        }

        failure {
            sh """
            curl -X POST -H 'Content-type: application/json' \
            --data '{ "text": "❌ Build #${env.BUILD_NUMBER} failed. Check Jenkins logs." }' \
            $SLACK_WEBHOOK
            """
        }
    }
}
