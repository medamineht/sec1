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
	stage('Kubernetes Deployment of ASG Bugg Web Application') {
	   steps {
	      withKubeConfig([credentialsId: 'kubelogin']) {
		  sh('kubectl delete all --all -n devsecops')
		  sh ('kubectl apply -f deployment.yaml --namespace=devsecops')
		}
	      }
   	}

	stage ('wait_for_testing'){
	   steps {
		   sh 'pwd; sleep 180; echo "Application Has been deployed on K8S"'
	   	}
	   }
	   
	stage('RunDASTUsingZAP') {
          steps {
		    withKubeConfig([credentialsId: 'kubelogin']) {
				sh('zap.sh -cmd -quickurl http://$(kubectl get services/asgbuggy --namespace=devsecops -o json| jq -r ".status.loadBalancer.ingress[] | .ip") -quickprogress -quickout ${WORKSPACE}/zap_report.html')
				archiveArtifacts artifacts: 'zap_report.html'
		    }
	     }
       } 
    }
}
