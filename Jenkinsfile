pipeline {
    agent any
    
    environment {
        TEST="test"
        REMOTE_HOST = '10.0.0.93'
        REMOTE_USER = 'seantrevor'
        REMOTE_PATH = '~/Projects/docker_containers'
    }
    
    stages {
        stage('Checkout') {
            steps {
                script {
                    // SSH into the remote server and pull the latest code
                    sh """
                        ssh ${REMOTE_USER}@${REMOTE_HOST} "cd ${REMOTE_PATH} && git pull"
                    """
                }
            }
        }
        
        stage('Find Changed Files') {
            steps {
                script {
                    // Run git diff to find the changed files on the remote server
                    def changedFiles = sh(script: """
                        ssh ${REMOTE_USER}@${REMOTE_HOST} "cd ${REMOTE_PATH} && git diff --name-only HEAD~1"
                    """, returnStdout: true).trim().split('\n')
                    def rootDirs = changedFiles.collect { it.split('/')[0] }.unique()
                    env.ROOT_DIRS = rootDirs.join(' ')
                }
            }
        }
        
        stage('Run Docker Compose') {
            steps {
                script {
                    echo "changed files: ${env.ROOT_DIRS}"
                    env.ROOT_DIRS.split(' ').each { dir ->
                        echo "Looking into: ${dir}"
                        if (fileExists("${dir}/docker-compose.yml")) {
                            // Run docker-compose on the remote server
                            sh """
                                ssh ${REMOTE_USER}@${REMOTE_HOST} "cd ${REMOTE_PATH}/${dir} && docker-compose up -d"
                            """
                        }
                    }
                }
            }
        }
    }
}
