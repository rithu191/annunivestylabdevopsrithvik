pipeline {
    agent any

    stages {
        stage('Environment') {
            steps {
                sh '''
                    set -eu
                    command -v java
                    command -v mvn
                    java -version
                    mvn -version
                    java_major="$(java -version 2>&1 | sed -n 's/.*version "\([^"]*\)".*/\1/p' | cut -d. -f1)"
                    if [ "$java_major" = "1" ]; then
                        java_major="$(java -version 2>&1 | sed -n 's/.*version "1\.\([0-9]*\).*/\1/p')"
                    fi
                    test "$java_major" -ge 17
                '''
            }
        }

        stage('Build') {
            steps {
                sh 'mvn -B clean compile'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn -B test'
            }
        }

        stage('Package') {
            steps {
                sh 'mvn -B package -DskipTests'
            }
        }
    }

    post {
        always {
            junit testResults: '**/target/surefire-reports/*.xml',
                  allowEmptyResults: true
        }

        success {
            archiveArtifacts artifacts: 'target/*.jar',
                             fingerprint: true
        }

        cleanup {
            deleteDir()
        }
    }
}