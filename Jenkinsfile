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
		IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
		IMAGE_TAG= "${RELEASE}-${BUILD_NUMBER}"
		JENKINS_API_TOKEN = credentials("JENKINS_API_TOKEN")
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
		
		stage("Trigger CD Pipeline") {
            steps {
                script {
                    sh "curl -v -k --user admin:${JENKINS_API_TOKEN} -X POST -H 'cache-control: no-cache' -H 'content-type: application/x-www-form-urlencoded' --data 'IMAGE_TAG=${IMAGE_TAG}' 'http://gitops.local:8080/job/gitops-complete-pipeline/buildWithParameters?token=gitops-token'"
                }
            }

        }
    }
}