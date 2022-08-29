pipeline{

      agent {
                docker {
                image 'maven'
		args '-v $HOME/.m2:/root/.m2'

                }
            }
        
        stages{

              stage('Quality Gate Status Check'){
                  steps{
                      script{
			      withSonarQubeEnv('sonarserver') { 
			      sh "mvn clean sonar:sonar"
                       	     	}
			      sleep(60)
			      timeout(time: 1, unit: 'MINUTES') {
			      def qg = waitForQualityGate()
				      print "fINISHED WAITING"
				      if (qg.status != 'OK') {
					   error "Pipeline aborted due to quality gate failure: ${qg.status}"
				      }
                    		}
		    	    sh "mvn clean install"
		  
                 	}
               	 }  
              }	
		
            }	       	     	         
}
