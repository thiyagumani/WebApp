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
                sh '"$MVN_HOME/bin/mvn" -f sonarqube-scanner-maven/pom.xml -Dmaven.test.failure.ignore clean package'
           }
         }	 
      }	
    }
}
