pipeline {
  agent any

  stages {
    stage('Builder') {
        steps {
            sh '''
            docker build --target builder -t pytest_builder \
                -f Dockerfile .
            '''
        }
    }


    stage('Tester') {
        steps {
            sh '''
            docker build --target tester -t pytest_tester \
                -f Dockerfile .
            '''
      }
    }

    stage('Deploy') {
        steps {
            sh '''
            docker build --target deploy -t pytest_deploy \
                -f Dockerfile .
            '''
        }
    }


    stage('Artifacts deployment') {
        steps {
            script {
                sh 'docker create --name tmp pytest_deploy'
                sh 'docker cp tmp:/packages $WORKSPACE/packages'
                sh 'docker rm tmp'
            }
            archiveArtifacts artifacts: 'packages/**/*', fingerprint: true
        }
    }


  }

  post {
    always {
      archiveArtifacts artifacts: 'test.log', fingerprint: true
    }
  }
}