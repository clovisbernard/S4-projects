pipeline {
    agent any 
    options {
        buildDiscarder(logRotator(numToKeepStr: '20'))
        disableConcurrentBuilds()
        timeout (time: 60, unit: 'MINUTES')
        timestamps()
      }
    environment {
    DOCKERHUB_CREDS = creds('dockerhub-creds')
    }
    stages {
         stage('SonarQube analysis') {
            agent {
                docker {
                  image 'sonarsource/sonar-scanner-cli:4.7.0'
                }
               }
               environment {
        CI = 'true'
        //  scannerHome = tool 'Sonar'
        scannerHome='/opt/sonar-scanner'
    }
            steps{
                withSonarQubeEnv('Sonar') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }
       stage('Login to docker hub') {
            steps {
                script {
                    sh '''
                    echo "${DOCKERHUB_CREDS_PSW}" | docker login --username "${DOCKERHUB_CREDS_USR}" --password-stdin
                    '''
                }
            }
        }

        stage('test') {
            steps {
                // Build the code using Maven
                sh 'ls'
            }
        }
    }
}