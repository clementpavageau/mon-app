pipeline {
  agent {
    docker {
      image 'node:20-alpine'
      args '-v /var/run/docker.sock:/var/run/docker.sock'
    }
  }

  environment {
    GITHUB_REPO = 'git@github.com:clementpavageau/mon-app.git'
    APP_NAME = 'mon-app'
    SSH_CRED_ID = 'github-ssh-key'
  }

  stages {

    stage('Install') {
      steps {
        sh 'npm install'
      }
    }

    stage('Build Static') {
      steps {
        sh 'npm run build'
      }
    }

    stage('Deploy') {
      steps {
        sshagent([SSH_CRED_ID]) {
          sh '''
          git config --global user.email "jenkins@ci"
          git config --global user.name "jenkins"

          git checkout --orphan gh-pages
          rm -rf .
          cp -r out/* .
          touch .nojekyll

          git add .
          git commit -m "deploy"
          git push -f ${GITHUB_REPO} gh-pages
          '''
        }
      }
    }
  }
}