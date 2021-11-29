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
                                docker build -t 52.146.144.231:8083/myapp:${VERSION} .
                                docker login -u admin -p $docker_password 52.146.144.231:8083 
                                docker push  52.146.144.231:8083/myapp:${VERSION} 
                                docker rmi 52.146.144.231:8083/myapp:${VERSION}
                             '''
                    }
                }
            }
      
      }
      
        stage('indentifying misconfigs using datree in helm charts'){
            steps{
                script{

                    dir('kubernetes/') 
                      {
                              sh 'helm datree test myapp/'
                        }
                    
                }
            }
        } 
    }  
 post {
		always {
			mail bcc: '', body: "<br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "${currentBuild.result} CI: Project name -> ${env.JOB_NAME}", to: "mayuryes1998@gmail.com";  
		 }
	   }
}


