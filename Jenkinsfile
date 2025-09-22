pipeline {
    agent any   // ✅ no Docker

    environment {
        VERCEL_TOKEN = credentials('vercel-token')
        SLACK_WEBHOOK = credentials('slack-webhook')
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Monica-mwangi/gallery.git'
            }
        }

        stage('Install dependencies') {
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
                sh 'npx vercel --token $VERCEL_TOKEN --prod --yes'
            }
        }
    }

    post {
        success {
            sh """
              curl -X POST -H 'Content-type: application/json' \
              --data '{"text":"✅ Build #${env.BUILD_NUMBER} deployed successfully!"}' \
              $SLACK_WEBHOOK
            """
        }
        failure {
            sh """
              curl -X POST -H 'Content-type: application/json' \
              --data '{"text":"❌ Build #${env.BUILD_NUMBER} failed. Check Jenkins logs."}' \
              $SLACK_WEBHOOK
            """
        }
    }
}
