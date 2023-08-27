pipeline {
    agent any
        options {
        buildDiscarder(logRotator(numToKeepStr: '20'))
        disableConcurrentBuilds()
        timeout (time: 60, unit: 'MINUTES')
        timestamps()
      }
        environment {
        DOCKERHUB_CREDS = credentials('dockerhub-creds') 
    }
    stages {
stage('Setup parameters') {
    steps {
        script {
            properties([
                parameters([
                    choice(
                        choices: ['DEV', 'QA', 'PREPROD'], 
                        name: 'ENVIRONMENT'
                    ),
                string(
                     defaultValue: '50',
                     name: 'auth_tag',
                     description: '''Please enter auth image tage to be used''',
                    ),

                string(
                     defaultValue: '50',
                     name: 'db_tag',
                     description: '''Please enter db  image tage to be used''',
                    ),

                string(
                     defaultValue: '50',
                     name: 'ui_tag',
                     description: '''Please enter ui image tage to be used''',
                    ),

                string(
                     defaultValue: '50',
                     name: 'weather_tag',
                     description: '''Please enter weather  image tage to be used''',
                    ),


                     string(name: 'WARNTIME',
                     defaultValue: '0',
                    description: '''Warning time (in minutes) before starting upgrade'''),

                string(
                     defaultValue: 'develop',
                     name: 'Please_leave_this_section_as_it_is',
                    ),


                ]),

            ])
        }
    }
}
        stage('warning') {
        steps {
            script {
                notifyUpgrade(currentBuild.currentResult, "WARNING")
                sleep(time:env.WARNTIME, unit:"MINUTES")
            }
        }
        }
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