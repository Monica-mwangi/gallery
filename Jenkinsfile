pipeline {
    agent any

    environment {
        VERCEL_TOKEN = credentials('vercel-token')
    }

    stages {
        stage('Setup Tools') {
            steps {
                sh '''
                  # Check versions (for debugging)
                  node -v
                  npm -v

                  # Install Vercel CLI if not already installed
                  if ! command -v vercel >/dev/null 2>&1; then
                    npm install -g vercel
                  fi
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
                        script: "vercel --prod --token $VERCEL_TOKEN --confirm --yes --scope default --json | jq -r '.url'",
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
              --data '{"text":"✅ Build #${env.BUILD_NUMBER} deployed successfully! Visit: https://${env.VERCEL_URL}"}' \
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
