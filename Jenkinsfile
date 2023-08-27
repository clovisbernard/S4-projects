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

        stage('Build auth') {
           when{  
            expression {
              env.ENVIRONMENT == 'DEV' }
              }
            steps {
                script {
                    // Log in to Docker Hub
                    sh '''
                        cd auth 
                        docker build -t devopseasylearning/s4-pipeline-auth:${BUILD_NUMBER} .
                        cd -
                    '''
                }
            }
        }


        stage('push auth ') {
           when{  
            expression {
              env.ENVIRONMENT == 'DEV' }
              }
            steps {
                script {
                    // Log in to Docker Hub
                    sh '''
                       
                        docker push devopseasylearning/s4-pipeline-auth:${BUILD_NUMBER} 
                        
                    '''
                }
            }
        }



        stage('Build db') {
           when{  
            expression {
              env.ENVIRONMENT == 'DEV' }
              }
            steps {
                script {
                    // Log in to Docker Hub
                    sh '''
                        cd DB
                        docker build -t devopseasylearning/s4-pipeline-db:${BUILD_NUMBER} .
                        cd -
                    '''
                }
            }
        }


        stage('push db ') {
           when{  
            expression {
              env.ENVIRONMENT == 'DEV' }
              }
            steps {
                script {
                    // Log in to Docker Hub
                    sh '''
                        
                        docker push devopseasylearning/s4-pipeline-db:${BUILD_NUMBER} 
                      
                    '''
                }
            }
        }

        stage('Build ui') {
           when{  
            expression {
              env.ENVIRONMENT == 'DEV' }
              }
            steps {
                script {
                    // Log in to Docker Hub
                    sh '''
                        cd UI
                        docker build -t devopseasylearning/s4-pipeline-ui:${BUILD_NUMBER} .
                        cd -
                    '''
                }
            }
        }


        stage('push ui ') {
           when{  
            expression {
              env.ENVIRONMENT == 'DEV' }
              }
            steps {
                script {
                    // Log in to Docker Hub
                    sh '''
                        docker push devopseasylearning/s4-pipeline-ui:${BUILD_NUMBER}
                      
                    '''
                }
            }
        }

        stage('Build weather') {
           when{  
            expression {
              env.ENVIRONMENT == 'DEV' }
              }
            steps {
                script {
                    // Log in to Docker Hub
                    sh '''
                        cd weather
                        docker build -t devopseasylearning/s4-pipeline-weather:${BUILD_NUMBER} .
                        cd -
                    '''
                }
            }
        }


        stage('push weather ') {
           when{  
            expression {
              env.ENVIRONMENT == 'DEV' }
              }
            steps {
                script {
                    // Log in to Docker Hub
                    sh '''
                        docker push devopseasylearning/s4-pipeline-weather:${BUILD_NUMBER}
                    '''
                }
            }
        }

   stage('Update QA  charts') {
      when{  
          expression {
            env.ENVIRONMENT == 'QA' }
          
            }
      
            steps {
                script {

                    sh '''
rm -rf S4-projects-charts || true
git clone git@github.com:devopseasylearning/S4-projects-charts.git
cd S4-projects-charts

cat << EOF > charts/weatherapp-auth/qa-values.yaml
image:
  repository: devopseasylearning/s4-pipeline-auth
  tag: qa-$auth_tag
EOF


cat << EOF > charts/weatherapp-mysql/qa-values.yaml
image:
  repository: devopseasylearning/s4-pipeline-db
  tag: qa-$db_tag
EOF

cat << EOF > charts/weatherapp-ui/qa-values.yaml
image:
  repository: devopseasylearning/s4-pipeline-ui
  tag: qa-$ui_tag
EOF

cat << EOF > charts/weatherapp-weather/qa-values.yaml
image:
  repository: devopseasylearning/s4-pipeline-weather
  tag: qa-$weather_tag
EOF


git config --global user.name "devopseasylearning"
git config --global user.email "info@devopseasylearning.com"

git add -A 
git commit -m "change from jenkins CI"
git push 
                    '''
                }
            }
        }

    }
   post {
   
   success {
      slackSend (channel: '#random', color: 'good', message: "SUCCESSFUL: Application weather-app  Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
    }

 
    unstable {
      slackSend (channel: '#random', color: 'warning', message: "UNSTABLE: Application weather-app  Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
    }

    failure {
      slackSend (channel: '#random', color: '#FF0000', message: "FAILURE: Application weather-app Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
    }
   
    cleanup {
      deleteDir()
    }
}

}