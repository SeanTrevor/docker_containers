pipeline {
    agent any

    stages {
        stage('Pull Latest Code') {
            steps {
                script {
                    sh 'pwd && cd /volume2/docker && git pull origin main'
                }
            }
        }
        stage('Build') {
            steps {
                echo 'Building...'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying...'
            }
        }
    }
}
