pipeline {
  agent any

  stages {
    stage('Checkout'){
      steps {
        git_url: 'https://github.com/phsinghka/jenkins-github-integration-practice.git', branch: 'java-app'
      }
    }

    stage('Build'){
      steps{
        sh 'mvn clean package'
      }
    }

    stage('Archive') {
      steps {
          archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
      }
    }
  }

  post {
    success {
        echo 'Build and tests succeeded!'
    }
    failure {
        echo 'Build or tests failed! '
    }
  }

}
