pipeline {
    agent any
        options {
        buildDiscarder(logRotator(numToKeepStr: '20'))
        disableConcurrentBuilds()
        timeout (time: 60, unit: 'MINUTES')
        timestamps()
      }
    stages {
         stage('SonarQube analysis') {
           when{  
            expression {
              env.ENVIRONMENT == 'DEV' }
              }
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
        stage('Docker Login') {
            steps {
            script {
                // Log in to Docker Hub
                sh '''
                    echo "${DOCKERHUB_CREDS_PSW}" | docker login --username "${DOCKERHUB_CREDS_USR}" --password-stdin
                '''
                }
            }
        }
    }
}