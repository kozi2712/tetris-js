pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                script {
                  sh 'docker volume prune -f'
                  git 'https://https://github.com/kozi2712/tetris-js.git'
                  sh 'git pull origin'
                  echo 'The repository was successfully cloned.'
                }
            }
        }
      stage('Install Dependencies'){
        steps {
                nodejs('Node20'){
                    echo 'Clean cache'
                }
            }
      }
        stage('Build') {
            steps {
                nodejs('Node20'){
                    echo 'Clean cache'
                    sh 'yarn cache clean'
                    sh 'yarn install'
                }
            }
        }
        stage('Run tests') {
            steps {
                nodejs('Node20'){
                sh 'yarn test'
                }
            }
        }
    }
}
