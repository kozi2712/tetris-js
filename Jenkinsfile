pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', 
                    branches: [[name: '*/master']], 
                    userRemoteConfigs: [[url: 'https://github.com/kozi2712/tetris-js']]])
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
                sh 'docker build -t doc-test -f dockerfile-test .'
            }
        }
    }
}
