pipeline {
    agent any
    
    parameters { 
         string(name: 'tomcat_dev', defaultValue: '18.234.91.40', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '54.224.40.119', description: 'Production Server')
    } 

    triggers {
         pollSCM('* * * * *') // Polling Source Control
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
                        sh "scp -o StrictHostKeyChecking=no -i /home/mauricio/aws-cert/newnvirginiakeypair.pem **/target/*.war ec2-user@${params.tomcat_dev}:/var/lib/tomcat/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "scp -o StrictHostKeyChecking=no -i /home/mauricio/aws-cert/newnvirginiakeypair.pem **/target/*.war ec2-user@${params.tomcat_prod}:/var/lib/tomcat/webapps"
                    }
                }
            }
        }
    }
}
