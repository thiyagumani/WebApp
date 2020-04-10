      pipeline {
        agent none
        tools {
          maven 'maven'
        }
        stages {
		  stage('Clone sources') {
                git url: 'https://github.com/midhunthampi/WebApp.git'
          }
		  
          stage("build & SonarQube analysis") {
            agent any
            steps {
              withSonarQubeEnv('sonar') {
                sh 'mvn clean package sonar:sonar -Dsonar.host.url=http://sonar-devops.westus.cloudapp.azure.com/ -Dsonar.login=a9c8113177c7cfd5d1fa326e88261b0e00f210d1 -Dsonar.sources=. -Dsonar.tests=. -Dsonar.test.inclusions=**/test/java/servlet/createpage_junit.java -Dsonar.exclusions=**/test/java/servlet/createpage_junit.java'
              }
            }
			post {
                 always {
                     jiraSendBuildInfo site: 'devopsgroups2.atlassian.net', branch: 'DEV-6 Implement Code'
                 }
             }
          }
          stage("Quality Gate") {
            steps {
              timeout(time: 1, unit: 'HOURS') {
                waitForQualityGate abortPipeline: true
              }
            }
          }
        }
      }
	  
	  
