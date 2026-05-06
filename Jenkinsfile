pipeline {
  agent any

  environment {
    GITHUB_REPO = 'git@github.com:clementpavageau/mon-app.git'
    APP_NAME = 'mon-app'
    SSH_CRED_ID = 'github-ssh-key'
  }

  stages {

    stage('Checkout') {
      steps {
        git branch: 'main', url: "${GITHUB_REPO}"
      }
    }

    stage('Build Docker') {
      steps {
        sh 'docker build -t mon-app .'
      }
    }

    stage('Install') {
      steps {
        sh 'docker run --rm mon-app npm ci'
      }
    }

    stage('Build Static') {
      steps {
        sh '''
        docker run --rm \
        -e NEXT_PUBLIC_BASE_PATH=/${APP_NAME} \
        mon-app npm run build
        '''
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