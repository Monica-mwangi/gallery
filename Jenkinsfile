pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                echo "📥 Checking out code"
                git branch: 'main', url: 'https://github.com/Monica-mwangi/gallery.git'
            }
        }
        stage('Install Dependencies') {
            steps {
                echo "📦 Installing npm packages"
                sh 'npm install'
            }
        }
        stage('Run Server') {
            steps {
                echo "🚀 Starting server"
                sh 'node server.js &'
            }
        }
    }
}
