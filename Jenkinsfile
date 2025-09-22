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
                sh 'npm run build'
            }
        }

        stage('Deploy to Vercel') {
            steps {
                script {
                    echo "Deploying to Vercel..."
                    env.VERCEL_URL = sh(
                        script: """
                            npx vercel --prod --token $VERCEL_TOKEN --confirm --json | jq -r '.url'
                        """,
                        returnStdout: true
                    ).trim()
                    echo "Deployed URL: https://${env.VERCEL_URL}"
                }
            }
        }
    }

    post {
        success {
            sh """
            curl -X POST -H 'Content-type: application/json' \
            --data '{"text":"✅ Build #${BUILD_NUMBER} deployed successfully! Visit: https://${VERCEL_URL}"}' \
            $SLACK_WEBHOOK
            """
        }

        failure {
            sh """
            curl -X POST -H 'Content-type: application/json' \
            --data '{"text":"❌ Build #${BUILD_NUMBER} failed. Check Jenkins logs."}' \
            $SLACK_WEBHOOK
            """
        }
    }
}
