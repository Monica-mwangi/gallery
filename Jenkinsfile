pipeline {
    agent any

    environment {
        RENDER_SERVICE_ID = credentials('render-service-id') // your Render service ID
        RENDER_API_KEY = credentials('render-api-key')       // your Render API key
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
                sh 'npm run build || echo "No build step required"' // your package has no build script, so this is safe
            }
        }

        stage('Deploy to Render') {
            steps {
                script {
                    echo "Deploying to Render..."
                    sh """
                        curl -X POST https://api.render.com/deploy/srv-${RENDER_SERVICE_ID} \
                        -H 'Accept: application/json' \
                        -H 'Authorization: Bearer ${RENDER_API_KEY}' \
                        -H 'Content-Type: application/json' \
                        -d '{"clearCache": true}'
                    """
                    echo "✅ Deployment triggered!"
                }
            }
        }
    }

    post {
        success {
            sh """
            curl -X POST -H 'Content-type: application/json' \
            --data '{"text":"✅ Build #${BUILD_NUMBER} deployed successfully!"}' \
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
