pipeline {
    agent any

    tools {
        // Uses the Node.js configuration from Jenkins Global Tool Configuration
        nodejs 'NodeJS-20' 
    }

    environment {
        // Define your target server details here
        REMOTE_USER = 'arijit'
        REMOTE_HOST = '192.168.29.171' // Replace with your target machine's IP or Domain
        TARGET_DIR  = '/home/arijit/server-files/node-hello-1'
    }

    stages {
        stage('Install Dependencies') {
            steps {
                echo 'Installing node modules...'
                sh 'npm install'
            }
        }

       

        stage('Deploy via SCP') {
            steps {
                echo 'Deployment stage triggered. Transferring all files via SCP using SSH Agent...'
                
                // Securely injects the SSH key from Jenkins Credentials Manager
                sshagent(['centos-private-key']) {
                    
                    // 1. Ensure the destination directory exists on the target machine
                    sh "ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${REMOTE_HOST} 'mkdir -p ${TARGET_DIR}'"
                    
                    // 2. Recursively SCP the entire workspace content to the target directory
                    sh "scp -r -o StrictHostKeyChecking=no ./* .[^.]* ${REMOTE_USER}@${REMOTE_HOST}:${TARGET_DIR}/"
                }
            }
        }
    }

    post {
        always {
            echo 'Wiping out the Jenkins agent workspace...'
            // This safely removes all files from the workspace directory on the Jenkins machine
            cleanWs()
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check console outputs above.'
        }
    }
}

