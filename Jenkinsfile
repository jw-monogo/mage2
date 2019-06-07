node {
  try {
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
    stage('Docker deploy'){
      sh 'docker run --network=logicvapes-uk-dev --name=lv_uk_dev_backend_php -d --rm lv_uk_dev/backend_php'
      sh 'docker run --network=logicvapes-uk-dev --name=lv_uk_dev_backend_nginx -d --rm lv_uk_dev/backend_nginx'

    }
  }
  catch (err) {
    throw err
  }
}
