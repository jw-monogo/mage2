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
      sh 'docker build -t lv_uk_dev/frontend_nginx -f docker/nginx/Dockerfile --no-cache .'
    }
    stage('Docker deploy'){
      sh 'docker run --rm lv_uk_dev/frontend_nginx --restart=unless-stopped --network=logicvapes-uk-dev'

    }
  }
  catch (err) {
    throw err
  }
}
