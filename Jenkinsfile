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
                    java_major="$(java -version 2>&1 | awk -F '"' '/version/ { split($2, version, "."); print (version[1] == "1" ? version[2] : version[1]); exit }')"
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