pipeline {
  agent any
  environment{
    docker_username = 'mathiasbrix'
  }

  stages {
    stage('clone down') {
      steps {
        stash excludes: '.git', name: 'code'
          }
        }
    stage('Parallel execution') {
      parallel {
        stage('Build') {
          steps {
            sh 'echo "hello world"'
          }
        }

        stage('Build app') {
          options {
            skipDefaultCheckout true
                  }
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          steps {
            unstash 'code'
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
            sh 'ls'
            stash 'build'
            deleteDir()
            sh 'ls'
          }
        }


        stage('test app') {
          options {
            skipDefaultCheckout true
                  }
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          steps {
            unstash 'code'
            sh 'ci/unit-test-app.sh'
            junit 'app/build/test-results/test/TEST-*.xml'
          }
        }

      }
    }
    stage('push docker app') {
      when { branch "master" }
      environment {
        DOCKERCREDS = credentials('docker_login') //use the credentials just created in this stage
                  }
      steps {
        unstash 'build' //unstash the repository code
        sh 'ci/build-docker.sh'
        sh 'echo "$DOCKERCREDS_PSW" | docker login -u "$DOCKERCREDS_USR" --password-stdin' //login to docker hub with the credentials above
        sh 'ci/push-docker.sh'
        }
    }
    stage('component test') {
      when {
        not {
          branch "dev/*"
            }
           }
      options {
        skipDefaultCheckout true
              }
      steps {
        unstash 'code'
        sh 'ci/component-test.sh'
      }
    }
  }
  post {
    always {
      deleteDir() /* clean up our workspace */
          }
        }
}
