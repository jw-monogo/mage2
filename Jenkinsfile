pipeline {
    agent any
    stages {
        stage("Checkout"){
            steps {
                checkout scm
            }
        }
        stage("Setenv"){
            steps {
                echo "${GIT_COMMIT}"
            }
        }
        stage('Build Docker php'){
            steps {
                sh 'docker build -t lv_uk_dev/backend_php -f docker/php/Dockerfile --no-cache .'
            }
        }
        stage('Docker deploy'){
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'ssh-monogo-tesla', keyFileVariable: 'SSH_KEY')]) {
                      sh 'ssh $SSH_TESLA_HOST -i $SSH_KEY docker-compose up -d --build -f /mnt/storage/containers/logicvapes/uk/dev/docker-compose.yml'
                }
            }
        }
    }
}
