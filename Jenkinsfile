pipeline {
    agent any 
    options {
        buildDiscarder(logRotator(numToKeepStr: '20'))
        disableConcurrentBuilds()
        timeout (time: 60, unit: 'MINUTES')
        timestamps()
      }

    stages {
        stage('test') {
            steps {
                // Build the code using Maven
                sh 'ls'
            }
        }
    }
}