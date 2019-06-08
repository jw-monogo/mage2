pipeline {
    agent any
    stages {
        stage("Checkout"){
            steps {
                checkout scm
                sh 'export TEST=$(echo test)'
                sh 'echo $TEST'
                echo "The head is on: ${GIT_COMMIT}"
            }
        }
        stage('Build PHP container'){
            environment {
                DOCKER_IMAGE = "lv_uk_dev/backend_php"
                DOCKERFILE_URL = "docker/php/Dockerfile"
            }
            steps {
                echo "Used docker image name: $DOCKER_IMAGE:$GIT_COMMIT"
                sh 'docker build -t $DOCKER_IMAGE:$GIT_COMMIT -f $DOCKERFILE_URL --no-cache .'
                echo "Tagging image as latest: $DOCKER_IMAGE:latest"
                sh 'docker tag $DOCKER_IMAGE:$GIT_COMMIT $DOCKER_IMAGE:latest'
            }
        }
        stage('Docker deploy'){
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'ssh-monogo-tesla', keyFileVariable: 'SSH_KEY')]) {
                      sh 'ssh $SSH_TESLA_HOST -i $SSH_KEY docker-compose -f /mnt/storage/containers/logicvapes/uk/dev/docker-compose.yml up -d --build'
                }
            }
        }
    }
}
