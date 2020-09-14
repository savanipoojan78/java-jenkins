def getDockerTag() {
 def tag = sh script: 'git rev-parse HEAD', returnStdout: true 
 return tag
}
pipeline{

      agent {
                docker {
                image 'maven'
                args '-v $HOME/.m2:/root/.m2'
                }
             }
      environment {
          Docker_tag = getDockerTag()
      }
        
        stages{

              stage('Quality Gate Status Check'){
                  steps{
                      script{
			      withSonarQubeEnv('Sonar') { 
			      sh "mvn sonar:sonar"
                       	     	}
			      timeout(time: 1, unit: 'HOURS') {
			      def qg = waitForQualityGate()
				      if (qg.status != 'OK') {
					   error "Pipeline aborted due to quality gate failure: ${qg.status}"
				      }
                    		}
		    	    sh "mvn clean install"
		  
                 	}
               	 }  
              }	

              stage('build'){
		      steps {
			      script{
                sh 'docker build . -t savani78/devops-training:$Docker_tag'
                withCredentials([string(credentialsId: 'docker_password', variable: 'docker_password')]) {
    
                sh '''docker login -u savani78 -p $docker_password
                docker push savani78/devops-training:$Docker_tag
		'''
                }
                
			      }
		      }
              }
		
            }	       	     	         
}