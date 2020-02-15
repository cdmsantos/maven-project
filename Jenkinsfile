pipeline {
    agent any

    tools {
        maven 'localMaven'
    }

    parameters {
         string(name: 'tomcat_dev', defaultValue: '127.0.0.1:8081', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '127.0.0.1:8082', description: 'Production Server')
    }

    triggers {
        pollSCM('* * * * *')
    }

    stages{
        stage('Build'){
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Staging'){
                    steps {
                        sh "cp -i **/target/*.war ${params.tomcat_dev}:/var/lib/tomcat7/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "cp -i **/target/*.war ${params.tomcat_prod}:/var/lib/tomcat7/webapps"
                    }
                }
            }
        }
    }
}