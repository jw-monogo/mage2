node {
  try {
    stage('Checkout') {
      checkout scm
    }
    environment {
    TAG = sh (
      returnStdout: true,
      script: 'git fetch --tags && git tag --points-at HEAD | awk NF'
    ).trim()
    }
    stage('Environment') {
      echo "Deploying to Prod ${TAG}"
      sh 'export IMG_NAME=$(git log -1 --pretty=%h)'
      sh '$IMG_NAME'
      sh 'printenv'
    }
    stage('Build Docker php'){
      sh 'docker build -t lv_uk_dev/backend_php -f docker/php/Dockerfile --no-cache .'
    }
    stage('Docker deploy'){
        withCredentials([sshUserPrivateKey(credentialsId: 'ssh-monogo-tesla', keyFileVariable: 'SSH_KEY')]) {
              sh 'ssh $SSH_TESLA_HOST -i $SSH_KEY docker-compose up -d --build -f /mnt/storage/containers/logicvapes/uk/dev/docker-compose.yml'
        }
    }
  }
  catch (err) {
    throw err
  }
}
