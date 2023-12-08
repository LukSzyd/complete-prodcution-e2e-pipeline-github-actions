pipeline {
    agent {
        label "testowy-agent"
    }
    tools{
        jdk 'Java17'
        maven 'Maven3'
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
    }
}