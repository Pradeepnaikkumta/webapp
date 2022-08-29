currentBuild.displayName = "Final_Demo # "+currentBuild.number

   def getDockerTag(){
        def tag = sh script: 'git rev-parse HEAD', returnStdout: true
        return tag
        }
        

pipeline{
        agent any  
        environment{
	    Docker_tag = getDockerTag()
        }
        
        stages{


              stage('Quality Gate Statuc Check'){

               agent {
                docker {
                image 'maven'
                args '-v $HOME/.m2:/root/.m2'
                }
            }
                  steps{
                      script{
                      withSonarQubeEnv('sonarserver') { 
                      sh "mvn sonar:sonar"
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



              stage('build')
                {
              steps{
                  script{
		 sh 'cp -r ../commit-based-jobs@2/target .'
                  sh 'docker build . -t pradeepnaikkumta/commit-based-jobs:$Docker_tag'
		  withCredentials([string(credentialsId: 'dps', variable: 'dockerpass')]) {
		  sh 'docker login -u pradeepnaikkumta -p $dockerpass'
		  sh 'docker push pradeepnaikkumta/commit-based-jobs:$Docker_tag'
			}
                       }
                    }
                 }
		 
		stage('ansible playbook'){
			steps{
			 	script{
				    sh '''final_tag=$(echo $Docker_tag | tr -d ' ')
				     echo ${final_tag}test
				     sed -i "s/docker_tag/$final_tag/g"  deployment.yaml
				     '''
				    ansiblePlaybook inventory: 'hosts' playbook: 'ansible.yaml'
				}
			}
		}
		
	
		
               }
	       
	       
	       
	      
    
}
