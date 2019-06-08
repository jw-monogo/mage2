pipeline {
    agent any
    stages {
        stage("Checkout"){
            steps {
                checkout scm
                echo "Recent commit is: ${GIT_COMMIT}"
            }
        }
        stage('Build PHP container'){
            environment {
                DOCKER_IMAGE = "lv_uk_dev/backend_php"
            }
            steps {
                sh 'docker build -t ${env.DOCKER_IMAGE}:$GIT_COMMIT -f docker/php/Dockerfile --no-cache .'
                sh 'docker tag ${env.DOCKER_IMAGE}:$GIT_COMMIT ${env.DOCKER_IMAGE}:latest'
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
