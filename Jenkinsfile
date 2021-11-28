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

                }
     
           }
        }
     stage("docker build & docker push"){
            steps{
                script{
                    withCredentials([string(credentialsId: 'docker_pass', variable: 'docker_password')]) {
                             sh '''
                                docker build -t 20.84.116.132:8083/springapp:${VERSION} .
                                docker login -u admin -p $docker_password 20.84.116.132:8083 
                                docker push  20.84.116.132:8083/springapp:${VERSION}
                                docker rmi 20.84.116.132:8083/springapp:${VERSION}
                                '''
                    }
                }
            }
        }   
    }
}