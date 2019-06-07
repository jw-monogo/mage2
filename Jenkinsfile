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
    stage('Build Docker nginx'){
      sh 'docker build -t lv_uk_dev/frontend_php -f docker/php/Dockerfile --no-cache .'
    }
    stage('Build Docker nginx'){
      sh 'docker build -t lv_uk_dev/frontend_nginx -f docker/nginx/Dockerfile --no-cache .'
    }
    stage('Docker deploy'){
      sh 'docker run --network=logicvapes-uk-dev --name=lv_uk_dev_frontend_nginx --rm lv_uk_dev/frontend_nginx'

    }
  }
  catch (err) {
    throw err
  }
}
