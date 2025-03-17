pipeline {
    agent any
    
    environment {
        GIT_REPO = 'git@github.com:SeanTrevor/docker_containers.git'
        BRANCH = 'main'  // Change this to your desired branch
    }
    
    stages {
        stage('Load Environment Variables') {
            steps {
                script {
                    def envVars = readProperties file: '.env'
                    env.DEPLOY_USER = envVars['DEPLOY_USER']
                    env.DEPLOY_HOST = envVars['DEPLOY_HOST']
                    env.DEPLOY_DIR = envVars['DEPLOY_DIR']
                }
            }
        }
        
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
