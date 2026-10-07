pipeline {
    agent any

    tools {
        nodejs 'NodeJS-20'
    }

    environment {
        REMOTE_USER = 'arijit'
        REMOTE_HOST = '192.168.29.171'
        TARGET_DIR  = '/home/arijit/server-files/node-hello-1'
    }

    stages {
        stage('Install Dependencies') {
            steps {
                echo 'Installing node modules...'
                sh 'npm install'
            }
        }

        stage('SonarQube Code Analysis') {
            steps {
                echo 'Starting Lightweight SonarQube Scanner Code Inspection...'
                
                // Uses the server profile configuration saved in Manage Jenkins -> System
                withSonarQubeEnv('SonarQube') {
                    // Executes the multi-line scanner script using a triple-quoted string
                    sh """
                        \${SCANNER_HOME}/bin/sonar-scanner \
                        -Dsonar.organization=AriChow1999 \
                        -Dsonar.projectKey=AriChow1999_node-hello-1 \
                        -Dsonar.projectName="node-hello-1" \
                        -Dsonar.sources=. \
                        -Dsonar.exclusions=node_modules/**,test/**,*.zip \
                        -Dsonar.javascript.node.maxspace=2048 \
                        -Dsonar.sourceEncoding=UTF-8
                    """
                }
            }
        }

        stage('Deploy via Rsync') {
            steps {
                echo 'Deployment stage triggered. Syncing files via Rsync...'

                sshagent(['centos-private-key']) {
                    // 1. Ensure target directory exists
                    sh "ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${REMOTE_HOST} 'mkdir -p ${TARGET_DIR}'"

                    // 2. Sync all files cleanly and safely manage permissions
                    sh """
                        rsync -avz -e "ssh -o StrictHostKeyChecking=no" \
                        --delete \
                        ./ ${REMOTE_USER}@${REMOTE_HOST}:${TARGET_DIR}/
                    """
                }
            }
        }
    }

    post {
        always {
            echo 'Wiping out the Jenkins agent workspace...'
            cleanWs()
        }
        success {
            echo 'Pipeline completed successfully! Server is synced.'
        }
        failure {
            echo 'Pipeline failed. Check the build console logs above.'
        }
    }
}

