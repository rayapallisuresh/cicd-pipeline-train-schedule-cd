pipeline{
    agent any
    stages{
        stage("Build"){
            steps{
                echo "Building the Train Schedule App"
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip', fingerprint: true
            }
            post{
                success{
                    echo "Build successfully"
                }
                failure{
                    echo "Build failed"
                }
            }
        }

        stage("DeployToStaging"){
            when {
                branch 'master'
            }
            steps{
                withCredentials([usernamePassword(credentialsId: 'webserver_login', passwordVariable: 'USERPASS', usernameVariable: 'USERNAME')]) {
                     sshPublisher(
                         failOnError: true,
                         continueOnError: false,
                         publishers: [
                             sshPublisherDesc(
                                              configName: 'Staging', 
                                              sshCredentials: [
                                                   encryptedPassphrase: "$USERPASS", 
                                                   username: "$USERNAME"
                                               ], 
                                               transfers: [
                                                   sshTransfer(
                                                       execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule', 
                                                       remoteDirectory: '/tmp', 
                                                       removePrefix: 'dist/', 
                                                       sourceFiles: 'dist/trainSchedule.zip'
                                                       )
                                                    ], 
                                            )
                                    ]
                                )
   
                }
            }
            
        }
        stage("DeployToProduction"){
            when {
                branch 'master'
            }
            steps{
                input {
                  message 'Does the staging env look ok?'
                }
                milestone(1)

                withCredentials([usernamePassword(credentialsId: 'webserver_login', passwordVariable: 'USERPASS', usernameVariable: 'USERNAME')]) {
                     sshPublisher(
                         failOnError: true,
                         continueOnError: false,
                         publishers: [
                             sshPublisherDesc(
                                              configName: 'Production', 
                                              sshCredentials: [
                                                   encryptedPassphrase: "$USERPASS", 
                                                   username: "$USERNAME"
                                               ], 
                                               transfers: [
                                                   sshTransfer(
                                                       execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule', 
                                                       remoteDirectory: '/tmp', 
                                                       removePrefix: 'dist/', 
                                                       sourceFiles: 'dist/trainSchedule.zip'
                                                       )
                                                    ], 
                                            )
                                    ]
                                )
   
                }
            }
            
        }
        
    }
    post{
        
        success{
            echo "========pipeline executed successfully ========"
        }
        failure{
            echo "========pipeline execution failed========"
        }
    }
}
