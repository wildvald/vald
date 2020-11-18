pipeline {
  agent any
  stages {
    stage('clean') {
      parallel {
        stage('clean') {
          steps {
            echo 'Clean'
          }
        }

        stage('deploy') {
          steps {
            echo 'deploy'
          }
        }

        stage('publish') {
          steps {
            echo 'publish'
          }
        }

      }
    }

    stage('build') {
      steps {
        echo 'build'
      }
    }

    stage('run') {
      steps {
        echo 'run'
      }
    }

    stage('done') {
      steps {
        echo 'done'
      }
    }

  }
}