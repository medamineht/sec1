pipeline {
  agent any
  tools { 
        maven 'Maven_3_6_3'  
    }
     stage('Build') { 
            steps { 
               withDockerRegistry([credentialsId: "", url: ""]) {
                 script{
                 app =  docker.build("hadjtaiebsofttodo/tt")
                 }
               }
            }
    }
     
      stage('Push') {
            steps {
                script{
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerlogin') {
                    app.push("latest")
                    }
                }
            }
    	}
  
}
