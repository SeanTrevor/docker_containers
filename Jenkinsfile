pipeline {
    agent any

    environment {
        REMOTE_USER = 'strevor'             // Remote server user
        REMOTE_HOST = '10.0.0.141'       // Remote server address
        REMOTE_PATH = '~/Projects/docker_containers'  // Path to project on remote server
        SSH_CREDENTIALS_ID = 'jenkins-ssh-key' // Jenkins SSH credentials ID
    }

    stages {
        stage('Pull Latest Code') {
            steps {
                script {
                    sshagent(['jenkins-ssh-key']) {
                        sh """
                            ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${REMOTE_HOST} "cd ${REMOTE_PATH} && git fetch origin main && git reset --hard origin/main"
                        """
                    }
                }
            }
        }

        stage('Detect Changed Directories') {
            steps {
                script {
                    env.CHANGED_DIRS = sh(script: """
                        ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${REMOTE_HOST} "cd ${REMOTE_PATH} && \
                        git diff --name-only HEAD@{1} HEAD | awk -F'/' '{print \$1}' | sort -u"
                    """, returnStdout: true).trim()
                }
            }
        }

        stage('Deploy Changed Services') {
            steps {
                script {
                    if (env.CHANGED_DIRS) {
                        sshagent(['jenkins-ssh-key']) {
                            sh """
                                ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${REMOTE_HOST} "cd ${REMOTE_PATH} && \
                                for dir in ${env.CHANGED_DIRS}; do \
                                    if [ -f \"\$dir/docker-compose.yml\" ]; then \
                                        cd \"\$dir\" && docker-compose up -d; \
                                    fi; \
                                done"
                            """
                        }
                    } else {
                        echo "No relevant changes detected. Skipping deployment."
                    }
                }
            }
        }
    }
}
