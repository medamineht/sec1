pipeline {
  agent any
  tools { 
        maven 'Maven_3_6_3'  
    }
   stages{
    stage('CompileandRunSonarAnalysis') {
            steps {	
		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=webapp -Dsonar.host.url=http://192.168.1.174:9000 -Dsonar.login=squ_c60a54cd4a644886d3c9931cd1dce4956e121930'
			}
        } 
    stage('RunSCAAnalysisUsingSnyk') {
            steps {		
				withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
					sh 'mvn snyk:test -fn'
				}
			}
    }
     stage('Build') { 
            steps { 
               withDockerRegistry([credentialsId: "", url: ""]) {
                 script{
                 app =  docker.build("tt")
                 }
               }
            }
    }
     
      stage('Push') {
            steps {
                script{
                    docker.withRegistry('https://hub.docker.com', 'dockerlogin') {
                    app.push("latest")
                    }
                }
            }
    	}
  }
}
