pipeline {
    agent any

    stages {
        stage('Builder') {
            steps {
                sh '''
                    docker build --target builder -t pytest_builder \
                        -f /var/jenkins_home/Dockerfiles/Dockerfile /var/jenkins_home
                '''
            }
        }

        stage('Tester') {
            steps {
                sh '''
                    docker build --target tester -t pytest_tester \
                        -f /var/jenkins_home/Dockerfiles/Dockerfile /var/jenkins_home
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker build --target deploy -t pytest_deploy \
                        -f /var/jenkins_home/Dockerfiles/Dockerfile /var/jenkins_home
                '''
            }
        }

        stage('Artifacts deployment') {
            steps {
                script {
                    sh 'docker create --name tmp pytest_deploy'
                    sh 'docker cp tmp:/packages $WORKSPACE/packages' // copy packages from container to Jenkins workspace
                    sh 'docker cp tmp:/app/test.log $WORKSPACE/test.log || echo "No test were found/run"> $WORKSPACE/test.log'
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
