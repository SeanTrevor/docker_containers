pipeline {
    agent any
    
    environment {
        GIT_REPO = 'git@github.com:SeanTrevor/docker_containers.git'
        BRANCH = 'main'  // Change this to your desired branch
        DEPLOY_USER="Sean"
        DEPLOY_HOST="10.0.0.172"
        DEPLOY_DIR="/volume2/docker/docker_containers"
    }
    
    stages {
        
        stage('Clone Repository') {
            steps {
                git branch: "$BRANCH", url: "$GIT_REPO"
            }
        }
        
        stage('Deploy to Remote Server') {
            steps {
                script {
                    sshagent(['STCloud-SSH-Deploy']) {
                        sh "ssh $DEPLOY_USER@$DEPLOY_HOST 'cd $DEPLOY_DIR && git pull origin $BRANCH'"
                    }
                }
            }
        }
    }
}
