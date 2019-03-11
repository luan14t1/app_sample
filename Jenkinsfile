pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh 'bundle check || bundle install --clean'
      }
    }
    stage('Deploy') {
      steps {
        echo 'abc'
      }
    }
  }
}