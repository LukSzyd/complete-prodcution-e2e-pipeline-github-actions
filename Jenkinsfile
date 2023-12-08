pipeline {
    agent {
        label "testowy-agent"
    }
    tools{
        jdk 'Java17'
        maven 'Maven3'
    }
	environment{
		APP_NAME = "complete-prodcution-e2e-pipeline-github-actions"
		RELEASE = "1.0.0"
		DOCKER_USER = "lukszyd"
		DOCKER_PASS = "docker_secret"
		IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP}"
		IMAGE_TAG= "${RELEASE}-${BUILD_NUMBER}"
	}
    stages {
        stage('Cleanup Workspace') { 
            steps {
                cleanWs();
            }
        }
		
		stage("Checkout from SCM"){
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/LukSzyd/complete-prodcution-e2e-pipeline-github-actions'
            }
        }
		
		stage("Build Application"){
            steps {
                sh "mvn clean package"
            }
        }
		
		stage("Test Application"){
            steps {
                sh "mvn test"
            }

        }
		
		stage("Sonarqube analysis"){
            steps {
				withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token', installationName: 'sonarqube-scanner'){
					sh "mvn sonar:sonar"
				}
            }
        }
		
		stage("Sonarqube Quality Gate"){
            steps {
				script{
					waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-sonarqube-token'
				}
            }
        }
		
		stage("Build & Push Docker Image"){
            steps {
				script{
					docker.withRegistry('',DOCKER_PASS){
						docker_image = docker.build "${IMAGE_NAME}"
					}
				 	
					docker.withRegistry('',DOCKER_PASS){
						docker_image.push("${IMAGE_TAG}")
						docker_image.push("latest")
					}
				}
            }
        }
    }
}