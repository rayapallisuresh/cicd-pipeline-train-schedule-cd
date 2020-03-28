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
