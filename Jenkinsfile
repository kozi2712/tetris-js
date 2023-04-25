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
                    sh 'docker volume create ent_vol'
                    sh 'docker build -t doc-build:latest -f dockerfile-build .'
                    sh 'docker run --mount type=volume,src="ent_vol",dst=/tetris_v1/deploy doc-build:latest bash -c "ls -l && cd .. && cp -r /tetris-js /tetris_v1/deploy"'
                }
            }
        }
        stage('Run tests') {
            steps {
                sh 'docker build -t doc-test -f dockerfile-test .'
            }
        }
      stage('Deploy') {
            steps {
                //sh 'docker rm -f doc-deploy || true'
		            sh 'docker run -dit --name doc-deploy --mount type=volume,src="ent_vol",dst=/dest node:latest'
                sh 'docker container exec doc-deploy sh -c "ls -l && cd dest && ls -l && cd tetris-js && ls -l"'
                sh 'docker container exec doc-deploy sh -c "cd dest/tetris-js && ls -l && yarn start"'
                sh 'docker container kill doc-deploy'
            }
        }
    }
}
