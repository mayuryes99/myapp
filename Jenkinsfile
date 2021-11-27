pipeline{
    agent any 
    environment{
        VERSION = "${env.BUILD_ID}"
    }
    stages{
        stage("sonar quality check"){
            agent {
                docker {
                    image 'openjdk:11'
                }
            }
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'sonar-password') {
                            sh 'chmod +x gradlew'
                            sh './gradlew sonarqube'
                    }
                    timeout(time: 1, unit: 'HOURS') {
                      def pg = waitForQualityGate()
                      if (pg.status != 'OK') {
                         error "Pipeline aborted due to quality gate failure: ${pg.status}"
                      }
                    }
                }
            }
        }
    }
}