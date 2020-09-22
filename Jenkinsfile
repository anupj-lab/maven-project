pipeline { 

    agent any 
    
	tools {
		maven  "Maven"
		}
		
    stages {
        
        stage("build") {
            steps {
                echo "Building an application using maven"
				sh 'mvn clean package'
				}
			post {
				sucess{
				echo "Artificat is generated succesfully"
				echo "Archiving an artifact"
				archiveArtifacts artifacts: '**/*.war', followSymlinks: false
				}
				failure{
					echo "Failed to generate an artifact"
					}
				}
			}
		
		
		stage ("Deploy to Staging and chking code analysis"){
		parallel {
		
			stage ("Deploy to Staging"){
				steps{
					deploy adapters: [tomcat8(credentialsId: '1ee3f9cc-2675-448f-b19b-7268da7b2fcb', path: '', url: 'http:\\\\ec2-3-12-111-24.us-east-2.compute.amazonaws.com:8888')], contextPath: null, onFailure: false, war: '**/*.war'
					}
				post {
					success {
						echo "Application is deployed sucecsfully on Tomcat in staging env"
							}
					failure {
						echo " Filed to deploy app. on tomcat server"
							}
					}
				}
		
		
			}
		
			stage("Perform code analysis""){
				steps {
					sh 'mvn checkstyle:checkstyle'
					}
				post {
					success {
						checkstyle canComputeNew: false, defaultEncoding: '', healthy: '', pattern: '', unHealthy: ''
							}
				
					}
					
					
			}
		
		}
		}
			
		stage("Deploy to production"){
			steps{
				timeout(time: 1, unit: 'DAYS'){
					input 'Do you want to deploy an application to production'
					}
		
				deploy adapters: [tomcat8(credentialsId: '1ee3f9cc-2675-448f-b19b-7268da7b2fcb', path: '', url: 'http:\\\\ec2-3-12-111-24.us-east-2.compute.amazonaws.com:9999')], contextPath: null, onFailure: false, war: '**/*.war'
			}
			post {
				success {
						echo "Application is deployed sucecsfully on Tomcat in Production env"
						}
				failure {
						echo " Filed to deploy app. on tomcat server in Production env"
						}
				
			}
	
		}
	}