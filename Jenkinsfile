pipeline {
    agent {
        // Define agent details here
    }
    environment {
        SSH_CREDENTIALS     = credentials('SSH_CREDENTIALS')
    }
    stages {
        stage('Checkout') {
          checkout scm
        }
        stage('Environment') {
            sh 'git --version'
            echo "Branch: ${env.BRANCH_NAME}"
            sh 'docker -v'
            sh 'printenv'
        }
        stage('Build Docker php'){
          sh 'docker build -t lv_uk_dev/backend_php -f docker/php/Dockerfile --no-cache .'
        }
        stage('Build Docker nginx'){
          sh 'docker build -t lv_uk_dev/backend_nginx -f docker/nginx/Dockerfile --no-cache .'
        }
    }
}
