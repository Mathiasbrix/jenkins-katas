pipeline {
  agent any
  stages {
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
            deleteDir()
            sh 'ls'

          }
        }
        stage('clone down') {
          steps {
            stash excludes: '/.git/', name: 'code'
          }
        }

      }
    }

  }
}
