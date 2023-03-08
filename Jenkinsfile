pipeline {
	agent any
	stages {
		stage("SCM") {
			steps {
				git 'https://github.com/himanshu27797/java-app.git'
				}
			}

		stage("build") {
			steps {
				sh 'sudo mvn dependency:purge-local-repository'
				sh 'sudo mvn clean package'
				}
			}
		stage("Image") {
			steps {
				sh 'sudo docker build -t java-repo:$BUILD_TAG .'
				sh 'sudo docker tag java-repo:$BUILD_TAG srronak/pipeline-java:$BUILD_TAG'
				}
			}
				
	
		
		stage("QAT Testing") {
			steps {
				sh 'sudo docker rm -f $(sudo docker ps -a -q)'
				sh 'sudo docker run -dit -p 9001:8080  srronak/pipeline-java:$BUILD_TAG'
				}
			}
		
		stage("Approval status") {
			steps {
				script {
					 Boolean userInput = input(id: 'Proceed1', message: 'Promote build?', parameters: [[$class: 'BooleanParameterDefinition', defaultValue: true, description: '', name: 'Please confirm you agree with this']])
                echo 'userInput: ' + userInput
					}
				}	
		}
		stage("Prod Env") {
			steps {
			 sshagent(['ec2-user']) {
			    sh 'ssh -o StrictHostKeyChecking=no ec2-user@13.235.83.237 sudo docker rm -f $(sudo docker ps -a -q)' 
	                    sh "ssh -o StrictHostKeyChecking=no ec2-user@13.235.83.237 sudo docker run  -d  -p  9001:8080  srronak/javatest-app:$BUILD_TAG"
				}
			}
		}
	}
}

