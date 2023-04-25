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
                    sh 'docker build -t doc-dep:latest -f dockerfile-dep .'
                }
            }
      }
        stage('Build') {
            steps {
                nodejs('Node20'){
                    sh 'docker build -t doc-build -f dockerfile-build .'
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
