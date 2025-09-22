pipeline {
    agent {
        docker {
            image 'node:18'   // ✅ Runs inside Node 18 container with npm & node
        }
    }

    environment {
        VERCEL_TOKEN = credentials('vercel-token')   // ✅ already in your setup
        SLACK_WEBHOOK = credentials('slack-webhook') // ✅ added Slack webhook
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/your-org/your-repo.git'
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
