pipeline {
    agent any 

    tools {
        nodejs 'NodeJS-20' 
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo 'Pulling the latest repository files...'
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                echo 'Installing project packages...'
                sh 'npm ci'
            }
        }
    }

    post {
        always {
            steps {
                echo 'Pipeline finished. Cleaning workspace...'
                cleanWs()
            }
        }
    }
}

