pipeline {
    agent any

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
                script {
                    // If you don’t have a build step, skip this
                    if (fileExists('package.json')) {
                        def pkg = readJSON file: 'package.json'
                        if (pkg.scripts?.build) {
                            sh 'npm run build'
                        } else {
                            echo "No build script defined, skipping build step."
                        }
                    }
                }
            }
        }

        stage('Deploy to Render') {
            steps {
                // Inject credentials for this stage
                withCredentials([
                    string(credentialsId: 'RENDER_API_KEY', variable: 'RENDER_API_KEY'),
                    string(credentialsId: 'RENDER_SERVICE_ID', variable: 'RENDER_SERVICE_ID')
                ]) {
                    script {
                        echo "Deploying to Render..."
                        sh """
                        curl -X POST https://api.render.com/v1/services/srv-${RENDER_SERVICE_ID}/deploys \
                        -H "Accept: application/json" \
                        -H "Authorization: Bearer ${RENDER_API_KEY}" \
                        -H "Content-Type: application/json"     
                        """
                        echo "✅ Deployment triggered!"
                    }
                }
            }
        }
    }

    post {
        // Inject Slack webhook for notifications
        always {
            withCredentials([string(credentialsId: 'SLACK_WEBHOOK', variable: 'SLACK_WEBHOOK')]) {
                script {
                    def statusMsg = currentBuild.currentResult == 'SUCCESS' ? 
                        "✅ Build #${BUILD_NUMBER} deployed successfully! https://gallery-83gn.onrender.com/?msg=File%20uploaded%20successfully":
                         
                        "❌ Build #${BUILD_NUMBER} failed. Check Jenkins logs."
                    
                    sh """
                    curl -X POST -H 'Content-type: application/json' \
                    --data '{"text":"${statusMsg}"}' \
                    $SLACK_WEBHOOK
                    """
                }
            }
        }
    }
}
