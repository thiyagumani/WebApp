pipeline {
    agent any
    stages {
        stage ('Clone') {
            steps {
                git branch: 'master', url: "https://github.com/midhunthampi/WebApp.git"
            }
        }
        
        stage('Sonarqube') {
           steps {
                withSonarQubeEnv(credentialsId: 'sonarqube1', installationName: 'sonarqube1')  {
                echo "Sonar Qube Code Analysis Completed"
           }
         }	 
		
    }
}
