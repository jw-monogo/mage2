pipeline {
    agent any
    environment {
        COMPOSER_CACHE_FILE = composer_cache_file_name()
        COMPOSER_VENDOR_CACHE_FILE = composer_vendor_cache_file_name()
    }
    stages {
        stage("Checkout"){
            steps {
                checkout scm
                echo "The head is on: ${GIT_COMMIT}"
            }
        }
        stage("Build Vendor"){
            environment {
                COMPOSER_CACHE_FOUND = composer_cache_file_exists()
            }
            steps {
                sh 'apk add composer'
                script {
                    if(env.COMPOSER_CACHE_FOUND.equals("false")) {
                        echo 'Vendor cache not found, creating...'
                        sh 'composer install --no-interaction --no-suggest --ignore-platform-reqs'
                        sh 'tar -zcf $COMPOSER_VENDOR_CACHE_FILE ./vendor && tar -zcf $COMPOSER_CACHE_FILE ~/.composer/cache'
                        withCredentials([sshUserPrivateKey(credentialsId: 'ssh-monogo-tesla', keyFileVariable: 'SSH_KEY')]) {
                           sh 'scp -i $SSH_KEY $COMPOSER_CACHE_FILE $SSH_TESLA_HOST:/mnt/storage/cache/'
                           sh 'scp -i $SSH_KEY $COMPOSER_VENDOR_CACHE_FILE $SSH_TESLA_HOST:/mnt/storage/cache/'
                        }
                        sh 'rm $COMPOSER_VENDOR_CACHE_FILE && rm $COMPOSER_CACHE_FILE'
                    }
                    else {
                        echo 'Vendor cache found, downloading...'
                        withCredentials([sshUserPrivateKey(credentialsId: 'ssh-monogo-tesla', keyFileVariable: 'SSH_KEY')]) {
                            sh 'scp -i $SSH_KEY $SSH_TESLA_HOST:/mnt/storage/cache/$COMPOSER_VENDOR_CACHE_FILE .'
                        }
                        sh 'mkdir -p vendor'
                        sh 'tar --strip=1 -zxf $COMPOSER_VENDOR_CACHE_FILE -C vendor'
                        sh 'rm $COMPOSER_VENDOR_CACHE_FILE'
                    }
                }
            }
        }
        stage('Build PHP container'){
            environment {
                COMPOSER_CACHE_FOUND = composer_cache_file_exists()
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

def composer_cache_file_name() {

    script {
        withCredentials([sshUserPrivateKey(credentialsId: 'ssh-monogo-tesla', keyFileVariable: 'SSH_KEY')]) {
            return sh(script : 'echo composer-logicvapes_uk_dev_backend-$(find composer.lock -type f | md5sum | awk \'{print $1}\').tar.gz', returnStdout: true).trim()
        }
    }
}

def composer_vendor_cache_file_name() {

    script {
        withCredentials([sshUserPrivateKey(credentialsId: 'ssh-monogo-tesla', keyFileVariable: 'SSH_KEY')]) {
            return sh(script : 'echo composer-logicvapes_uk_dev_backend-vendor-$(find composer.lock -type f | md5sum | awk \'{print $1}\').tar.gz', returnStdout: true).trim()
        }
    }
}

def composer_cache_file_exists() {
    script {
        withCredentials([sshUserPrivateKey(credentialsId: 'ssh-monogo-tesla', keyFileVariable: 'SSH_KEY')]) {
            return sh(script: 'ssh $SSH_TESLA_HOST -i $SSH_KEY test -f /mnt/storage/cache/$COMPOSER_CACHE_FILE && echo true || echo false', returnStdout: true).trim()
        }
    }
}
