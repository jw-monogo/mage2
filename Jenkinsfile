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
                COMPOSER_CACHE_FILE = composer_cache_file_name()
                COMPOSER_CACHE_FOUND = composer_cache_file_exists()
                DOCKER_IMAGE = "lv_uk_dev/backend_php"
                DOCKERFILE_URL = "docker/php/Dockerfile"
            }
            steps {
                sh 'echo $COMPOSER_CACHE_FILE'
                sh 'echo $COMPOSER_CACHE_FOUND'
                withCredentials([sshUserPrivateKey(credentialsId: 'ssh-monogo-tesla', keyFileVariable: 'SSH_KEY')]) {
                   sh 'ssh $SSH_TESLA_HOST -i $SSH_KEY docker-compose -f /mnt/storage/containers/logicvapes/uk/dev/docker-compose.yml up -d --build'
                }
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

def composer_cache_file_name() {

    script {
        withCredentials([sshUserPrivateKey(credentialsId: 'ssh-monogo-tesla', keyFileVariable: 'SSH_KEY')]) {
            return sh(script : "echo composer-logicvapes_uk_dev_backend-$(find composer.lock -type f | md5sum | awk '{print $1}').tar.gz", returnStdout: true).trim()
        }
    }
}

def composer_cache_file_exists() {
    script {
        withCredentials([sshUserPrivateKey(credentialsId: 'ssh-monogo-tesla', keyFileVariable: 'SSH_KEY')]) {
            return sh(script: 'ssh $SSH_TESLA_HOST -i $SSH_KEY test -f "/mnt/storage/cache/$COMPOSER_CACHE_FILE" && echo true || echo false', returnStdout: true).trim()
        }
    }
}
