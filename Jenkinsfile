pipeline {
    agent any
  parameters{
    string(
      name: 'VERSION', 
      defaultValue: '1.0.0', 
      description: '')
    booleanParam(
      name: 'PROMOTE', 
      defaultValue: true, 
      description: '')
  }
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
                    sh 'docker rmi -f doc-dep || true'
                    echo 'Clean cache'
                    sh 'docker build -t doc-dep:latest -f dockerfile-dep .'
                }
            }
      }
        stage('Build') {
            steps {
                nodejs('Node20'){
                    sh 'docker rmi -f doc-build || true'
                    sh 'docker volume create ent_vol'
                    sh 'docker build -t doc-build:latest -f dockerfile-build .'
                    sh 'docker run --mount type=volume,src="ent_vol",dst=/tetris_v1/deploy doc-build:latest bash -c "ls -l && cd .. && cp -r /tetris-js /tetris_v1/deploy"'
                }
            }
        }
        stage('Run tests') {
            steps {
                sh 'docker rmi -f doc-test || true'
                sh 'docker build -t doc-test -f dockerfile-test .'
            }
        }
      stage('Deploy') {
            steps {
                sh 'docker rm -f doc-deploy || true'
		            sh 'docker run -dit --name doc-deploy --mount type=volume,src="ent_vol",dst=/dest node:latest'
                sh 'docker container exec doc-deploy sh -c "ls -l && cd dest && ls -l && cd tetris-js && ls -l"'
                //sh 'docker container exec doc-deploy sh -c "cd dest/tetris-js && ls -l && yarn start"'
                //sh 'docker container kill doc-deploy'
            }
        }
      stage('Publish') {
            steps {
              script{
                if(params.PROMOTE){
                  sh "docker rm -f doc-pub || true"
                  sh 'docker rmi -f doc-publish || true'
                  sh "rm -rf ${params.VERSION} || true"

                  sh "mkdir ${params.VERSION}"

                  sh 'docker build --no-cache -t doc-publish:latest . -f dockerfile-publish'
                  sh "docker run -dit --name doc-pub doc-publish:latest"
                  sh 'docker container exec doc-pub sh -c "ls -l && cd .. && ls -l && cd .. && ls -l"'
                  sh "docker cp doc-pub:/tetris-js/tetris-js-0.1.0.tgz ./${params.VERSION}"


                  withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'pswd', usernameVariable: 'user')]) {
                    sh "docker login -u jankoz -p ${pswd}"
                  }
                  sh "docker tag doc-publish:latest jankoz/tetris-js:${params.VERSION}"
                  sh "docker push jankoz/tetris-js:${params.VERSION}"
                }
              }
              else{
                sh 'echo "bad publish"'
              }
            }
          }
        }
    }
}
