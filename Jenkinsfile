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
    }
}