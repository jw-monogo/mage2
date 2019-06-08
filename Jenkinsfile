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

    stage('Docker deploy'){
        withCredentials([sshUserPrivateKey(credentialsId: 'ssh-monogo-tesla', keyFileVariable: 'SSH_KEY')]) {
              sh 'ssh $SSH_TESLA_HOST -p $SSH_KEY ls -l / '
        }
    }
  }
  catch (err) {
    throw err
  }
}
